# Week6



### Docker

 是一個開源的容器化平台

* `容器化`：Docker 可以將應用程序及其依賴項打包到一個輕量級的可移植容器中，使得應用程序可以在不同的環境中運行，而不必擔心底層的系統差異。
* `易用性`：Docker 提供了一個簡單易用的命令行界面，使得開發人員可以快速創建、啟動、停止和管理容器。
* `隔離性`：Docker 容器提供了一個隔離的運行環境，使得不同的應用程序可以在同一台主機上運行而互不干擾。
* `可擴展性`：Docker 容器可以在分佈式環境中快速部署和擴展，使得應用程序可以更加靈活和高效地運行。
* `易移植性`：Docker 容器可以在任何支持 Docker 的環境中運行，而不需要進行修改或調整。

注意 : 

* 依規格封裝成為一層一層的架構，新的變動堆疊在舊的上面，需要這個安裝或設定好的系統時，由底下一層一層執行
* 一個容器最好只做一件事，不要做太多複雜的事，要做複雜的事，就產生多個容器來共同執行
* 容器要有一個程式一直重複執行(例如bash、shell)，不然程式一結束，容器也會跟著結束



> 環境建置

* [參考網址](https://docs.docker.com/engine/install/centos/)

1. 確保虛擬機內沒有 Docker，有的話移除掉

   ```sh
   yum remove docker \
                     docker-client \
                     docker-client-latest \
                     docker-common \
                     docker-latest \
                     docker-latest-logrotate \
                     docker-logrotate \
                     docker-engine
   ```

2. 安裝

   ```sh
   yum install -y yum-utils
   ```

   ```sh
   yum-config-manager \
       --add-repo \
       https://download.docker.com/linux/centos/docker-ce.repo
   ```

   ```sh
   yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
   ```

3. 開啟 Docker

   ```sh
   systemctl start docker
   ```
   
4. 抓取 Docker的鏡像檔

   ```dockerfile
   docker pull centos:centos7.9.2009
   ```

5. Docker 執行

   * 若不指定要使用哪個鏡像檔，預設則是centos:latest，若沒有預設，則會自動到官網下載
   * `exit` 離開 Docker 容器

   ```do
   docker run centos:centos7.9.2009 echo "Hello"
   ```

6. 查看 Docker 環境中運行或已停止的 Docker 容器的詳細信息

   * `-a` 參數代表 "all"，用於顯示所有容器，包括已停止的容器。如果沒有使用 `-a` 參數，`docker ps` 命令僅顯示正在運行的容器。

   ```dockerfile
   docker ps -a
   ```

   ![](D:\大學\大三\大三下\Linux系統自動化運維\note\picture\week6\docker-1.jpg)

7. 刪除已停止的 Docker 容器

   ```dockerfile
   docker rm 767
   ```

8. 查看 Docker 環境中運行的 Docker 容器

   ```dockerfile
   docker ps
   ```

9. 啟動一個 busybox 映像的容器

   * `docker run -it --name b1 busybox sh` 給一個b1的名字讓其名稱縮短，較好操作
     * 若建立的容器名稱相衝突，則無法建立

   ```dockerfile
   docker run -it --name b1 busybox sh
   ```

10. 在容器內做一些事

    ```sh
    echo "hello" > hello.txt
    ```

    ```sh
    cat hello.txt
    ```

11. 開新的 Terminal 也啟動一個 busybox 映像的容器

    ```dockerfile
    docker run -it busybox sh
    ```

12. 再開一個新的 Terminal 查看 Docker 環境中運行的 Docker 容器

    ```dockerfile
    docker ps
    ```

    ![](D:\大學\大三\大三下\Linux系統自動化運維\note\picture\week6\docker-2.jpg)

13. 產生新的鏡像檔

    * 這樣才能保留在 docker 容器內執行的程式，否則一關掉則會丟失

    * b1為要被指定複製的 docker 名稱，busybox:0.1為新的鏡像檔名稱

    ```dockerfile
    docker commit b1 busybox:0.1
    ```

14. 檢查全部的 Docker 容器的 ID

    ```dockerfile
    docker ps -a -q
    ```

15. 刪除全部的 Docker 容器

    * 無法刪除正在運行的 Docker

    * ```dockerfile
      docker rm `docker ps -a -q` -f # 加上 -f 參數可以連正在運行的 Docker都刪除
    
    ```dockerfile
    docker rm `docker ps -a -q`
    ```

> 利用 Docker 執行 python

1. 抓下一個帶有 python3 的鏡像檔

   ```dockerfile
   docker pull python:3.9.16-slim
   ```

2. 創建 test-python3 資料夾

   ```sh
   mkdir test-python3
   ```

3. 進入 test-python3 資料夾

   ```sh
   cd test-python3
   ```

4. 寫一個 python 的程式

   ```sh
   vim test.py
   ```

   ```python
   print("Hello World!")
   ```

5. 查看資料夾路徑

   ```sh
   pwd
   ```

6. 使用 Docker 容器來執行 python 程式

   * `-v` 代表將外面的資料夾映射到 Docker 內 

   ```dockerfile
   docker run -it -v /root/test-python3:/tmp python:3.9.16-slim bash
   ```

7. 切換到 tmp 資料夾下

   ```dockerfile
   cd /tmp
   ```

8. 執行 test.py

   ```dockerfile
   python test.py
   ```

   ![](D:\大學\大三\大三下\Linux系統自動化運維\note\picture\week6\docker-3.jpg)

> 利用 Docker 架設網頁伺服器

1. 下載鏡像檔

   ```dockerfile
   docker run -it --name web centos:7.9.2009 bash
   ```

2. 安裝 httpd

   ```sh
   yum install httpd -y
   ```

2. 切換到另一個Terminal，將一個名為 "web" 的容器，轉換成一個新的映像檔並命名為 "centos:web"

   ```dockerfile
   docker commit web centos:web
   ```

   
