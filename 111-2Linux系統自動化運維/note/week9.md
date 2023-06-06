# Week9



### DockerFile

* 包含了從基礎映像開始構建Docker映像所需的所有步驟。
* 這樣可以確保映像是可重現的，因為任何人都可以使用相同的Dockerfile來構建相同的映像。

1. 建立 test-dockerfile 資料夾

   ```sh
   mkdir test-dockerfile
   ```

2. 進入 test-dockerfile 資料夾

   ```ls
   cd test-dockerfile
   ```

3. 建立 DockerFile 檔案

   * D 要大寫

   ```sh
   gedit Dockerfile
   ```

   ```sh
   FROM centos:7.9.2009
   RUN yum -y install httpd
   EXPOSE 80
   ADD index.html /var/www/html
   ```

4. 建立首頁

   ```sh
   echo "Roy" > index.html
   ```

5. 執行 Docker

   ```sh
   systemctl start docker
   ```

6. 執行建構

   * `.` 代表第3步建立的 Dockerfile

   ```dockerfile
   docker build -t roy:httpd .
   ```

7. 查看鏡像檔

   ```dockerfile
   docker images
   ```

8. 執行鏡像檔

   ```dockerfile
   docker run -d -p 8888:80 roy:httpd /usr/sbin/apachectl -DFOREGROUND
   ```

9. 列出當前正在運行的 Docker 容器

   ```dockerfile
   docker ps
   ```

   ![](D:\大學\大三\大三下\111-2Linux系統自動化運維\note\picture\week9\dockerserver-1.jpg)

10. 查看IP

    ```sh
    ifconfig
    ```

11. 在瀏覽器上輸入 IP位置

    * 範例 : 192.168.198.134:8888

    ![](D:\大學\大三\大三下\111-2Linux系統自動化運維\note\picture\week9\dockerserver-2.jpg)



### Docker-Compose

* 用於管理多個 Docker 容器的應用程序
* 提供一個方便的方式來管理和運行多個 Docker 容器，使得在多個容器之間建立聯繫變得更加容易和自動化

1. 下載stable release的docker-compose binary

   ```sh
   curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   ```

2. 給予執行權限

   ```sh
   chmod +x /usr/local/bin/docker-compose
   ```

3. 測試安裝是否完成

   ```sh
   docker-compose --version
   ```

4. 建立 test-dockercompose 資料夾

   ```sh
   mkdir test-dockercompose
   ```

5. 進入 test-dockercompose 資料夾

   ```sh
   cd test-dockercompose
   ```

6. 建立 docker-compose.yml 檔案

   ```sh
   gedit docker-compose.yml
   ```

   ```sh
   version: '3'
   services:
     web:
       image: "roy:httpd"
       ports: 
         - "8899:80"
       command: "/usr/sbin/apachectl -DFOREGROUND"
   ```

7. 執行 docker-compose

   ```dockerfile
   docker-compose up -d
   ```

8. 查看運行中的 docker-compose

   ```dockerfile
   docker-compose ps
   ```

9. 查看IP

   ```sh
   ifconfig
   ```

10. 在瀏覽器上輸入 IP位置

    * 範例 : 192.168.198.134:8899

    ![](D:\大學\大三\大三下\111-2Linux系統自動化運維\note\picture\week9\dockercompose-1.jpg)

11. 停止 docker-compose

    * 要在使用 `docker-compose up` 的資料夾下使用才能執行

    ```dockerfile
    docker-compose down
    ```

    

### JumpServer

* 開源的基於Web的SSH控制台，提供集中和安全的遠程服務器和設備訪問
* 可紀錄帳號密碼，用於管理機器
* 可記錄每個帳號分別做過的事和時間，可錄影
* 可設定讓某些危險指令無法使用，例如 `rm -f`
* 可製作防火牆，只允許從 jumpserver 連入
* 手機網路無法負荷

1. 下載安裝

   ```sh
   git clone --depth=1 https://github.com/wojiushixiaobai/Dockerfile.git
   ```

   ```sh
   cd Dockerfile
   ```

   ```sh
   cp config_example.conf .env
   ```

   ```sh
   docker-compose -f docker-compose-network.yml -f docker-compose-redis.yml -f docker-compose-mariadb.yml -f docker-compose-init-db.yml up -d
   ```

   ```sh
   docker exec -i jms_core bash -c './jms upgrade_db'
   ```

   ```sh
   docker-compose -f docker-compose-network.yml -f docker-compose-redis.yml -f docker-compose-mariadb.yml -f docker-compose.yml up -d
   ```

   ![](D:\大學\大三\大三下\111-2Linux系統自動化運維\note\picture\week9\jumpserver-1.jpg)

2. 確認 80 埠沒被占用

   * 在第一步的最後一個指令前使用

   ```sh
   netstat -tunlp | grep 80
   ```

3. 查看IP

   ```sh
   ifconfig
   ```

4. 在瀏覽器上輸入 IP位置

   * 範例 : 192.168.198.134

5. 初始登入

   ```
   用戶名 : admin
   密碼 : admin
   ```

   * 第一次需要更改

   ```
   用戶名 : admin
   密碼 : 123456
   ```

   ![](D:\大學\大三\大三下\111-2Linux系統自動化運維\note\picture\week9\jumpserver-2.jpg)

> 系統簡介

* Administrator(系統管理者) : 進行管理配置設定、稽核，所有權力他都有

* Audlit(稽核者) : 查看誰做了什麼事

* User(一般使用者)

  * 創建新使用者

    ```
    Name : Tom             # 可以不用跟虛擬機上的名字一樣
    Username : Tom
    Email : tom@abc.com
    
    Password : 123456      # 要6位數
    MFA : Disable          # MFA : 多重認證
    System roles : User
    Is active : open
    ```

* Assets(資產) : 需要管理的東西

  * 創建新資產

    ```
    Hostname : centos7-2
    IP(HOST) : 192.168.198.133   # 需要被管理的虛擬機IP而非jumpserver所在的虛擬機IP
    Platform : Linux             # 可管理不同作業系統，例如Windows、路由器、交換機等等 
    Protocols : ssh 22埠         # 進行遠端登入的埠號
    Node : Default               # 一般節點
    ```

* System Users

  * create

    ```
    Name : user
    Protocol : SSH
    Username : user
    passward : user
    ```

* Asset permissions

  * create

    ```
    Name : TomForCentos7-2
    ```

  
