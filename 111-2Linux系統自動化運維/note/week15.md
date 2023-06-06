# Week15



### ansible

* 列出 Ansible 中的主機清單

  ```sh
  ansible servers --list hosts
  ```

* 查看 Ansible 的幫助文檔

  ```sh
  ansible --help
  ```

* 主機上執行 `id user | grep group` 查找使用者所屬的群組信息

  ```sh
  ansible servers -m shell -a "id user | grep group"
  ```

* 執行 `ifconfig | grep -A 5 ens33` ，以查找名為 "ens33" 的網絡接口的詳細信息。

  ```sh
  ansible servers -m shell -a "ifconfig | grep -A 5 ens33"
  ```

* 執行 `echo hi > /tmp/a.txt` 命令，將 "hi" 寫入到 `/tmp/a.txt` 文件中

  * 有 `>` 就要使用 `shell`  

  ```sh
  ansible servers -m shell -a "echo hi > /tmp/a.txt"
  ```

* 查看 Shell 模塊的相關文檔

  ```sh
  ansible-doc shell
  ```

* 執行 `chdir=/tmp creates=/etc/passwd echo hi`，將在 `/tmp` 資料夾執行，並檢查是否存在檔案`/etc/passwd` 。如果該檔案存在則不會執行命令，因為使用了 `creates` 參數。這有助於避免重複執行，如果該檔案不存在，則執行 `echo hi` 

  ```sh
  ansible servers -m shell -a "chdir=/tmp creates=/etc/passwd echo hi"
  ```

* 執行 `chdir=/tmp removes=/etc/passwd echo hi` 命令，將在 `/tmp` 資料夾執行，並檢查是否存在檔案 `/etc/passwd` 。如果該檔案存在，則執行 `echo hi` 

  ```
  ansible servers -m shell -a "chdir=/tmp removes=/etc/passwd echo hi"
  ```

* 在遠程主機上執行本地主機上的腳本文件。通過指定腳本的路徑和參數，您可以在遠程主機上執行腳本並處理相應的操作

  ```sh
  ansible server1 -m script -a "./a.sh"
  ```

* 在遠程主機上執行本地主機上的腳本文件。通過指定腳本的路徑和參數，您可以在遠程主機上執行腳本並處理相應的操作

  ```sh
  ansible server1 -m script -a "./b.sh"
  ```

  * b.sh

    ```sh
    result=`ifconfig | grep -A 5 ens33 | grep inet | grep -v inet6 | awk '{print $2}'`
    echo $result
    ```

* 遠端安裝

  ```sh
  ansible server1 -m yum -a "name=httpd state=latest"
  ```

* 遠端查詢是否安裝 httpd

  ```sh
  ansible server1 -m shell -a "rpm -qa | grep httpd"
  ```

* 遠端移除軟體

  ```
  ansible server1 -m yum -a "name=httpd state=absent"
  ```

* 遠端複製

  * `backup=yes` 若是檔案已經存在則會進行備份

  ```sh
  ansible server1 -m copy -a "src=/root/1.txt dest=/tmp/hi.txt backup=yes mode=664 owner=user group=user"
  ```

* 從遠端抓檔案到本地

  ```sh
  ansible server1 -m fetch -a "src=/etc/passwd dest=/root"
  ```

* 創建了一個名為 "test.txt" 的檔案，並將其所有者設為 "user"，所屬群組設置為 "user"，檔案權限設為 666。

  ```sh
  ansible server1 -m file -a "path=/tmp/test.txt owner=user group=user mode=666"
  ```

* 修改資料夾屬性

  ```sh
  ansible server1 -m file -a "path=/tmp/testdir mode=700"
  ```

* 創建了一個 `1.txt` 的連結，連結到 `/tmp/testdir` 目錄。

  ```sh
  ansible server1 -m file -a "src=/tmp/testdir name=/root/1.txt state=link"
  ```

* 遠端開啟 Httpd Server

  ```sh
  ansible server1 -m service -a "name=httpd state=started"
  ```

* 本地訪問目標 IP 地址

  ```sh
  curl 192.168.198.135
  ```

* 新增群組

  ```sh
  ansible server1 -m group -a "name=testgroup"
  ```

* 群組新增使用者

  ```sh
  ansible server1 -m user -a "name=testuser group=testgroup uid=1100 comment='peter' home=/home/testuser"
  ```

* 刪除使用者

  ```sh
  ansible server1 -m user -a "name=testuser state=absent"
  ```

* 刪除群組

  ```sh
  ansible server1 -m group -a "name=testgroup state=absent"
  ```

* 測試使用者有沒有存在(2個方法)

  ```sh
  ansible server1 -m user -a "id user"
  ```

  ```sh
  ansible server1 -m user -a "getent passwd user"
  ```

* 同時安裝

  ```sh
  ansible server1 -m yum -a "name=httpd,vsftpd state=present"
  ```

* 同時查詢兩個套件是否安裝成功

  * `&&`  第一個成功才會執行第二個
  * `||`  第一個成功則不會執行第二個
  * `;`  兩個都會執行

  ```sh
  ansible server1 -m shell -a "rpm-qa | grep httpd; rpm -qa | grep vsftp"
  ```
  
  

> 練習

* 將埠號改成8080

  1. 將檔案拷貝回本地
  
     ```sh
     ansible server1 -m fetch -a "src=/etc/httpd/conf/httpd.conf dest=/root"
     ```
  
  2. 編輯 `httpd.conf` 將 port 改成 8080
  
     ```sh
     vim 192.168.198.135/etc/httpd/conf/httpd.conf
     ```
  
     ```sh
     listen 8080
     ```
  
  3. 將檔案複製回去
  
     ```sh
     ansible server1 -m copy -a "src=/root/192.168.198.135/etc/httpd/conf/httpd.conf dest=/etc/httpd/conf/httpd.conf"
     ```
  
  4. 遠端重啟 server1 的 httpd
  
     ```sh
     ansible server1 -m service -a "name=httpd state=restarted"
     ```
  
  5. 查看 server1 的 8080 port
  
     ```sh
     curl 192.168.198.135:8080
     ```
  
     
