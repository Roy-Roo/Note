# Week7



### DOCKER

> 開啟 Server

1. 開啟Docker

   ```sh
   systemctl start docker
   ```

2. 檢查本地端的8000端口有沒有被使用

   ```sh
   netstat -tunlp | grep 8000
   ```

3. 創建一個基於CentOS的Docker容器，啟動Apache Web Server，並將主機的8000端口映射到容器的80端口

   * `-d` : 將容器設置為在後台運行
   * `-p 8000:80`: 將主機（Host）的8000端口映射到容器的80端口

   ```sh
   docker run -d -p 8000:80 centos:web /usr/sbin/apachectl -DFOREGROUND
   ```

4. 在瀏覽器上輸入本地端的IP以及埠號(8000)

   ```sh
   ifconfig
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week7/dockerserver-1.jpg)

> 新增網頁到網站中

* 方法 1(不建議使用)

1. 進入到正在運行的 web容器中

   * f61 為正在運行的容器的ID，要先使用`docker ps` 來確認

   ```dockerfile
   docker exec -it f61 bash
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week7/dockerserver-2.jpg)

2. 進入到容器內的 /var/www/html 資料夾

   ```dockerfile
   cd /var/www/html 
   ```

3. 在資料夾內新增 hi.htm 檔案

   ```dockerfile
   echo "Hello" > hi.htm
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week7/dockerserver-3.jpg)

4. 在瀏覽器上輸入本地端的IP以及埠號(8000)和/hi.htm

   * 範例

   ```
   http://192.168.198.134:8000/hi.htm 
   ```

* 方法 2

1.  在本地端創建一個 myweb 資料夾

   ```sh
   mkdir myweb
   ```

2. 進入 myweb 資料夾

   ```sh
   cd myweb
   ```

3. 在資料夾內新增 hi.htm 檔案

   ```sh
   echo "Hello World" > hi.htm
   ```

4. 確認 myweb 資料夾的路徑

   ```sh
   pwd
   ```

5. 啟動 Docker 容器，在後台運行Apache Web伺服器，該伺服器將監聽容器內部的80端口，並且將允許外部用戶端透過主機的8000端口訪問，將映射容器內部的/var/www/html目錄到主機上的/home/user/myweb目錄

   ```dockerfile
   docker run -d -p 8000:80 -v /home/user/myweb:/var/www/html --name web1 centos:web /usr/sbin/apachectl -DFOREGROUND
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week7/dockerserver-4.jpg)

6. 在瀏覽器上輸入本地端的IP以及埠號(8000)和/hi.htm

   * 範例

   ```
   http://192.168.198.134:8000/hi.htm 
   ```

7. 只要更改埠號和name即可創建出具有相同功能的新網站

   ```dockerfile
   docker run -d -p 8001:80 -v /home/user/myweb:/var/www/html --name web2 centos:web /usr/sbin/apachectl -DFOREGROUND
   ```

   ```
   docker run -d -p 8002:80 -v /home/user/myweb:/var/www/html --name web3 centos:web /usr/sbin/apachectl -DFOREGROUND
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week7/dockerserver-5.jpg)

> 利用腳本來創建多個具有相同功能的網頁

1. 創建腳本 create_docker_httpd.sh &

   ```sh
   gedit create_docker_httpd.sh &
   ```

   ```sh
   #!/usr/bin/bash
   
   for i in {1..5}
   do
       portno=`expr 8000 + $i`
       docker run -d -p $portno:80 -v /home/user/myweb:/var/www/html centos:web /usr/sbin/apachectl -DFOREGROUND
   done
   ```

2. 給予腳本權限

   ```sh
   chmod +x create_docker_httpd.sh
   ```

3. 執行腳本

   ```sh
   ./create_docker_httpd.sh
   ```

### 負載均衡器(Load balancer)

* 使用haproxy

1. 安裝haproxy

   ```sh
   yum install haproxy openssl-devel -y
   ```

2. 切換到 /etc/haproxy/ 的資料夾內

   ```sh
   cd /etc/haproxy/ 
   ```

3. 編輯 haproxy.cfg 檔

   ```sh
   gedit haproxy.cfg &
   ```

   * 將原先的內容全部刪除，改成以下的設定，IP和埠號要更改成自己的

   ```sh
   defaults
     mode http
     timeout client 10s
     timeout connect 5s
     timeout server 10s
     timeout http-request 10s
   
   frontend myfrontend
     bind 0.0.0.0:8080
     default_backend myservers
   
   backend myservers
     balance roundrobin
     server server1 192.168.198.134:8001
     server server2 192.168.198.134:8002
     server server3 192.168.198.134:8003
     server server4 192.168.198.134:8004
     server server5 192.168.198.134:8005
   ```

4. 開啟 haproxy

   ```sh
   systemctl start haproxy
   ```

5. 創建 myweb1~myweb5 共5個資料夾

   ```sh
   mkdir myweb{1..5}
   ```

6. 到各個myweb資料夾生成 hi.htm 檔案

   ```sh
   echo "web1" > hi.htm
   ```

   ```sh
   echo "web2" > hi.htm
   ```

   ```sh
   echo "web3" > hi.htm
   ```

   ```sh
   echo "web4" > hi.htm
   ```

   ```sh
   echo "web5" > hi.htm
   ```

7. 修改之前的腳本

   ```sh
   gedit create_docker_httpd.sh &
   ```

   ```sh
   #!/usr/bin/bash
   
   for i in {1..5}
   do
       portno=`expr 8000 + $i`
       docker run -d -p $portno:80 -v /home/user/myweb$i:/var/www/html centos:web /usr/sbin/apachectl -DFOREGROUND
   done
   ```

8. 開啟之前的腳本創建多個 Server

   ```sh
   ./create_docker_httpd.sh
   ```

9. 在瀏覽器上輸入本地端的IP以及埠號(8080)和/hi.htm，重新整理網頁即可

   * 範例

   ```
   http://192.168.198.134:8080/hi.htm
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week7/dockerserver-6.jpg)

### Docker Hub

* `docker images` : 列出所有的鏡像檔
* `docker rmi centos:web` : 刪除名為 web 的鏡像檔

1. 先在 [Docker Hub](https://hub.docker.com/) 註冊

   * 要到信箱按下認證

2. 在 Terminal 登入 Docker Hub

   * 要輸入帳密

   ```dockerfile
   docker login
   ```

3. 給予要上傳的鏡像檔新的名稱

   * 541為要上傳的鏡像檔的ID，要先用`docker images` 確認ID

   ```dockerfile
   docker tag 541 royroy1215/centos:web
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week7/dockerserver-7.jpg)

4. 將鏡像檔備份到雲端

   ```dockerfile
   docker push royroy1215/centos:web
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week7/dockerserver-8.jpg)
