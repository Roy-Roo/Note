# Week 5



### DNS Server



* 要連到 ***www.a.com***

  1. 電腦先到 /etc/hosts 檢查有沒有 www.a.com 對應的IP
  2. 若沒有，再檢查 DNS cache 進行搜尋，若有查詢過，在本地端會有一份備份
  3. 若沒有，才會用 DNS Server 進行查詢  www.a.com 的IP

  * DNS Server 使用的埠號為 53 ，使用的協定為 UDP

* 每一台在網路上的電腦都有一個IP位址
* Domain Name
  * 利用較有意義的名字代表一個IP位址
* 專門回答名稱與IP的對應查詢結果的軟體或主機稱為 Domain Name Server
* 以最頂端的 ***根網域 `root`*** 為中心，向下發展的樹狀結構
* 根網域 root 的名稱為 `.` 
* 每個網域名稱的最上層都是根網域，名稱的最後都會有個`.`，DNS Server在處理時會自動加上，並未強制規定要加`.`
* 根網域下的第一層子網域(Top-Level Domains， TLD，頂層網域)
  * DNS 初期有7個TLD
    * `.com` : 商業機構
    * `.edu` : 教育機構和研究機構
    * `.gov` : 美國政府機構
    * `.int` : 國際機構
    * `.mil` : 軍事單位
    * `.net` : 提供和管理網路基礎結構的機構
    * `.org` : 非商業機構
* 一個完整的網域名稱(Fully Qualified Domain Name，FQDN)
  * 由實際的電腦名稱、網域名稱、TLD名稱構成
  * 由於每個網域名稱的最上層皆屬於根網域，DNS Server或瀏覽器在查詢過程中，會幫我們加上被省略掉的`.`

> 運作方式

* 資源紀錄

  * 主管每個網域的權限管轄DNS Server都會有一份用來儲存這個網域資料的`網域資料庫檔`
  * 每個資料庫檔案儲存的是有關這個網域的`資源紀錄`
  * 一個名稱對應一個IP位址的 `A紀錄`
    * 一個A紀錄對應IPV4，四個A紀錄對應IPV6

* 紀錄的類型

  | 紀錄類型 | 意義                   | 值                               |
  | -------- | ---------------------- | -------------------------------- |
  | SOA      | 權限開始(權限有效期限) | 網域的參數                       |
  | NS       | DNS Server             | 此網域的DNS Server名稱           |
  | MX       | 郵件交換器             | 此網域的郵件伺服器名稱和優先程度 |
  | A        | 位址                   | 電腦的IP位址                     |
  | PTR      | 指標                   | 電腦的名稱                       |
  | CNAME    | 標準名稱               | 電腦的別名                       |

> DNS的分層負責

* DNS以分層負責的方式運作，最上層為根網域，可用`.` 作為代表
* 世界各地共有13台根網域的主機，會記錄根網域DNS的資源紀錄 、1級的DNS Server所在的位址

> 查詢指令

*  `dig`

  * ex.

    ```sh
    dig www.nqu.edu.tw
    ```

    ```sh
    dif @8.8.8.8 www.nqu.edu.tw
    ```

* `host`

  * ex.

    ```
    host csie.nqu.edu.tw 8.8.8.8
    ```



### 架設安裝 BIND(Berkeley Internet Name Domain)

1. 檢查53號埠有沒有被使用

   ```sh
   netstat -tunlp | grep 53
   ```

   ![](D:\大學\大三\大三下\Linux系統自動化運維\note\picture\week5\DNS Server - 1.jpg)

   ```sh
   # 被使用的話使用以下指令刪除
   kill -9 6101 # 上圖 LISTEN 右邊的數字
   ```

2. 檢查 firewalld 、enforce 有沒有關上

   ```sh
   getenforce
   # Disabled
   ```

   ```sh
   systemctl status firewalld
   # inactive
   ```

   ```sh
   # 永久關上 enforce
   gedit /etc/selinux/config
   # SELINUX=disabled
   reboot
   setenforce 0 
   ```

   ```sh
   # 永久關上 firewalld
   sytemctl disable firewalld
   sytemctl stop firewalld
   ```

3. 在 Windows 上 Ping Linux 的IP試試看能否成功

   ```sh
   # 在虛擬機內
   ifconfig
   # 在Windos命令提示字元
   Ping ens38的IP位址
   ```

4.  安裝 bind、bind-chroot、bind-utils 

   ```sh
   yum install bind bind-chroot bind-utils -y
   ```

5. 開啟配置檔

   ```sh
   gedit /etc/named.conf
   ```

   ```sh
   	listen-on port 53 { any; };
   	
   	allow-query     { any; };
   ```

6. 啟動 Server

   ```sh
   systemctl restart named
   ```

7. 在 Windows 的 cmd 中進行查詢

   ```sh
   nslookup www.nqu.edu.tw 192.168.86.130
   # 前為想查詢的網站 、 後為虛擬機的IP位址
   ```

   ![](D:\大學\大三\大三下\Linux系統自動化運維\note\picture\week5\DNS Server - 2.jpg)

> 管理網域 : 正向解析

8. 切換到 /var/named 目錄

   ```sh
   cd /var/named
   ```

9. 編輯 a.com.zone

   ```sh
   gedit a.com.zone
   ```

   ```sh
   $TTL 600 ;10 minutes
   
   @ IN SOA	@ s110910519@student.nqu.edu.tw ( # 此處改為自己的gmail
   		2021031803 ;serial
   		10800      ;refresh
   		900        ;retry
   		604800     ;expire
   		86400      ;minimum
   		)
   @		NS    dns1.a.com.
   dns.com.	A     192.168.198.130 # 虛擬機所在的IP位址
   dns1		A     192.168.198.130 # 同上為虛擬機所在的IP位址
   www		A     192.168.198.100 # 以下第3個198，都要跟本機(上面的dns.com.)所在的網路一樣
   eshop		CNAME www
   ftp		A     192.168.198.200
   abc		A     192.168.198.150
   ```

10. 編輯 /etc/named.rfc1912.zones 在最後增加以下

    ```sh
    gedit /etc/named.rfc1912.zones
    ```

    ```sh
    zone "a.com" IN {
    	type master;
    	file "a.com.zone";
    	allow-update { none; };
    };
    ```

11. 檢查配置檔是否有問題

    ```sh
    named-checkconf
    ```

12. 重啟 named

    ```sh
    systemctl restart named
    ```

13. 在Windows的cmd中進行查詢

    ```sh
    nslookup www.a.com 192.168.198.130 
    # 前為想查詢的網站 、 後為詢問的伺服器位址(虛擬機的IP)
    ```

    ![](D:\大學\大三\大三下\Linux系統自動化運維\note\picture\week5\DNS Server - 3.jpg)

> 管理網域 : 反向解析

14. 編輯 /etc/named.rfc1912.zones 在最後增加以下

    ```sh
    gedit /etc/named.rfc1912.zones
    ```

    ```sh
    zone "198.168.192.in-addr.arpa" IN { # 第一個數要改成自己所在的網域的第三個數
    	type master;
    	file "198.168.192.in-addr.arpa.zone"; # 複製此處的198.168.192.in-addr.arpa.zone，等等會用到
    	allow-update { none; };
    };
    ```

15. 編輯 /var/named/198.168.192.in-addr.arpa.zone

    ```sh
    gedit /var/named/198.168.192.in-addr.arpa.zone
    ```

    ```sh
    @ IN SOA	@ ramy1231863.gmail.com (
    		2021031803 ;serial
    		10800      ;refresh
    		900        ;retry
    		604800     ;expire
    		86400      ;minimum
    		)
    # 以下兩行第一個數都要改成自己所在的網域
    198.168.192.in-addr.arpa.    IN  NS dns1.a.com.
    198.168.192.in-addr.arpa.    IN  NS dns2.a.com.
    # 以下兩行第二個數都要改成自己所在的網域
    100.198.168.192.in-addr.arpa. IN PTR www.a.com.
    150.198.168.192.in-addr.arpa. IN PTR abc.a.com.
    200.198.168.192.in-addr.arpa. IN PTR ftp.a.com.
    ```

16. 檢查配置檔是否有問題

    ```sh
    named-checkconf
    ```

17. 重啟 named

    ```sh
    systemctl restart named
    ```

18. 在 Windows 的 cmd 中進行查詢

    ```sh
    nslookup 192.168.198.100 192.168.198.130
    # 前為想查詢的網站 、 後為詢問的伺服器位址(虛擬機的IP)
    ```

    ![](D:\大學\大三\大三下\Linux系統自動化運維\note\picture\week5\DNS Server - 4.jpg)