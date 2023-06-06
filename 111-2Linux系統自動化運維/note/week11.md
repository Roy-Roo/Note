# Week11



### 監控

* 常見監控軟體 : `nagios` 、 `zabbix`  、  `prometheus`

* 通常會有 Server / Cilent / Proxy(有時候)
* 被監控的機器不需要安裝客戶端，只需要透過外在測試和觀察，就可以監控
* 最簡單的監控方式 : 送 ping(不須安裝任何軟體) 、 送封包來確認port有無開啟

> Report(回報) : 

* active(主動式) : Client 會自動定期回報資料給 Server
* passive(被動式) : Server 會定期詢問拿取資料

> Proxy(代理人) : 

* Cilent 先回報給 Proxy, 再由 Proxy 回報給 Server 來進行管理

> Notify :

* 可設定臨界值，若是超標(CPU不足、磁碟過載...)就自動傳訊息通知(Line, Email)負責人



### zabbix

> Linux 使用 Gmail 寄信

1. 申請應用程式的密碼 [google application](https://accounts.google.com/signin/v2/challenge/pwd?TL=AG7eRGBC90AI0avzia3tk3BKNikl43pIRdQVlP-Wn17b8K4UNqG0NXT--mSG-CSc&cid=1&continue=https%3A%2F%2Fmyaccount.google.com%2Fapppasswords%3Fpli%3D1&flowName=GlifWebSignIn&ifkv=AQMjQ7Rb6G-SW9arLFVrVsozSYlLWL0mA7La0tPANx3AnR3SPT1o0KsTHKQ_fDElSi_bMeqQmFnVfw&rart=ANgoxcdxUvXg2fG67GbUbGGp5rBP4QL2tEtdWLC-P4Vp5YuaaFQFJtcivZ1phHGDrB_VoDqKtxEzTDyCBa6_FdgvtK-8EgDiSQ&sarp=1&scc=1&service=accountsettings&flowEntry=ServiceLogin)，選擇應用 -> 其他，打上隨便一個內容後按下生成就會拿到密碼將其紀錄下來

   * Gmail 帳號要有雙重認證，否則安全性太低會無法使用

   ```
   XXXXXXXXXXXXXXXX
   ```

2. 編輯 Linux 的 Mail 的設定

   ```sh
   gedit /etc/mail.rc
   ```

   * 移動到最下面(需要更改幾個地方)

   ```sh
   set smtp-use-starttls
   set ssl-verify=ignore
   set nss-config-dir=/etc/pki/nssdb/
   set from=901217roy@gmail.com             # 設定自己的寄件者信箱
   set smtp=smtp://smtp.gmail.com:587
   set smtp-auth-user=901217roy@gmail.com   # 設定 Gmail_Smtp 認證帳號
   set smtp-auth-password=XXXXXXXXXXXXXXXX  # 設定 Google 應用程式密碼(第一步取得的密碼)
   set smtp-auth=login
   ```

   * 測試(寄到第二個信箱)

   ```sh
   echo "hello world" | mail -v -s "Test1234" s110910519@student.nqu.edu.tw 
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week11/zabbix-1.jpg)

> zabbix 做監控系統

* 在第一台虛擬機(監控用)做

1. 安裝 Maria DB

   ```sh
   yum install mariadb-server mariadb -y
   ```

2. 啟動 Mariadb

   ```sh
   systemctl start mariadb
   ```

3. 設定安全腳本

   * 除了設定root密碼外，其他選項Disallow root和Remove test選n，其他都Enter跳過

   ```sh
   mysql_secure_installation
   ```

   ```
   passwd --> 123456
   ```

4. root 登入 Mysql

   ```sh
   mysql -u root -p
   ```

   ```
   passwd --> 123456
   ```

5. 建立一個新的資料庫並授權一個使用者可以使用該資料庫

   * 創建一個名為 zabbix_db 的新資料庫，並設定該資料庫的編碼為 UTF-8

   ```mysql
   create database zabbix_db character set utf8 collate utf8_bin;
   ```

   * 創建一個新的 MySQL 使用者帳號 zabbix，並設定該帳號的密碼為 password。@localhost 則代表這個帳號只能從本地主機進行登入。

   ```mysql
   create user zabbix@localhost identified by 'password';
   ```

   * 授權剛剛建立的 zabbix 使用者帳號可以使用 zabbix_db 資料庫的全部權限，這包括讀取、寫入、更新、刪除、創建資料表等操作。

   ```mysql
   grant all privileges on zabbix_db.* to zabbix@localhost;
   ```

   * 完成後離開

   ```mysql
   quit;
   ```

6. 下載 Zabbix

   * 到 [zabbix官網](https://www.zabbix.com/)下載 [5.0LTS centos7的版本](https://www.zabbix.com/download?zabbix=5.0&os_distribution=centos&os_version=7&components=server_frontend_agent&db=mysql&ws=apache)
   * Linux 下載盡量選 LTS(Long Tern Support 長期維護)版本

   ```sh
   rpm -Uvh https://repo.zabbix.com/zabbix/5.0/rhel/7/x86_64/zabbix-release-5.0-1.el7.noarch.rpm
   ```

   ```sh
   yum clean all
   ```

   ```sh
   yum install zabbix-server-mysql zabbix-agent -y
   ```

   ```sh
   yum install centos-release-scl -y
   ```

   ```sh
   vim /etc/yum.repos.d/zabbix.repo
   ```

   * 把 enable改成1

   ```
   [zabbix-frontend]
   ...
   enabled=1
   ...
   ```

7. 安裝前端軟體

   ```
   yum install zabbix-web-mysql-scl zabbix-apache-conf-scl -y
   ```

8. 導入 Zabbix 數據，並輸入剛剛設定的密碼`password`

   ```sh
   zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix_db
   ```

9. 修改帳號密碼

   ```sh
   vim /etc/zabbix/zabbix_server.conf
   ```

   * 新增DBPassword和修改DBName

   ```sh
   DBName=zabbix_db    # 100行
   DBUser=zabbix       # 116行
   DBPassword=password # 125行
   ```

10. 修改時區

    * 設定時區必須要把前面的分號拿掉

    ```sh
    vim /etc/opt/rh/rh-php72/php-fpm.d/zabbix.conf
    ```

    ```sh
    php_value[date.timezone] = Asia/Taipei
    ```

11. 重啟 Zabbix

    ```sh
    systemctl restart zabbix-server zabbix-agent httpd rh-php72-php-fpm
    ```

    ```sh
    systemctl enable zabbix-server zabbix-agent httpd rh-php72-php-fpm
    ```

12. 在瀏覽器上輸入第一台虛擬機的IP

    * 192.168.198.134/zabbix

    ```sh
    ifconfig
    ```

    ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week11/zabbix-2.jpg)

* Zabbix 設定

1. next step

2. 注意每一項都要是 OK 即可下一步

3. 設置

   ```
   Database type : MySQL
   Database host : localhost
   Database port : 0
   Database name : zabbix_db
   User : zabbix
   Password : password
   ```

4. 設置完成後即可重複下一步到結束

5. 登入

   ```
   Username : Admin
   Password : zabbix
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week11/zabbix-3.jpg)

* Client端(要兩台)

1. 下載 Zabbix

   ```sh
   yum install https://repo.zabbix.com/zabbix/5.0/rhel/7/x86_64/zabbix-release-$ 5.0-1.el7.noarch.rpm -y
   ```

   ```sh
   yum install vim zabbix-agent zabbix-sender -y
   ```

2. 修改配置

   ```sh
   vim /etc/zabbix/zabbix_agentd.conf
   ```

   ```
   Server=192.168.198.134                # 伺服器(第一台虛擬機)的IP, 117行
   ServerActive=192.168.198.134          # 伺服器(第一台虛擬機)的IP, 163行
   Hostname=Clone of Clone of CentOS 7-2 # 目前客戶端的主機叫做什麼就改成什麼, 174行
   ```

3. 啟動 Zabbix

   ```sh
   systemctl start zabbix-agent
   ```

4. 設定開機時啟動

   ```sh
   systemctl enable zabbix-agent
   ```

5. 查看 Zabbix 狀態

   ```sh
   systemctl status zabbix-agent
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week11/zabbix-4.jpg)

6. 查看 Zabbix 使用的 Port

   ```
   netstat -tunlp | grep zabbix
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week11/zabbix-5.jpg)

7. Configuration -> Hosts -> Create -> 以下設定好後按 Add

   * Host

   ```
   Host name : clone of CentOS 7-2   # 要被監控的虛擬機的名字
   
   Groups : Linux servers
   Interfaces : 
   	Agent : 192.168.198.133       # 要被監控的虛擬機的字IP
   ```

   * Templates(模板,有預設的監控項)

   ```
   Link new templates : Template OS Linux by Zabbix agent
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week11/zabbix-6.jpg)

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week11/zabbix-7.jpg)

8. 重複第七步將第三台虛擬機放到 Zabbix 上 ![](D:\大學\大三\大三下\111-2Linux系統自動化運維\note\picture\week11\zabbix-8.jpg)

* 測試

1. 在 Server 端上安裝手動測試 Client 端的套件

   ```sh
   yum install zabbix-get -y
   ```

2. 測試(詢問client hostname)

   ```sh
   zabbix_get -s 192.168.198.133 -p 10050 -k "system.hostname"
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week11/zabbix-9.jpg)

   * ZBX 要亮起綠色的才有成功

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week11/zabbix-10.jpg)
