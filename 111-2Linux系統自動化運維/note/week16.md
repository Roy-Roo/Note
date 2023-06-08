# Week16



### ansible playbooks

* setup : 收集主機的訊息

  ```sh
  ansible server1 -m setup 
  ```

  ```sh
  ansible server1 -m setup | grep -A 3 -i "IP"
  ```

  ```sh
  ansible server1 -m setup | grep -A 3 -i "os"
  ```

  ```sh
  ansible server1 -m setup | grep -A 3 -i "memory"
  ```

  

* ansible 的腳本檔

> 範例 1 : 顯示 Hello World

1. 編輯 `hello.yml` 檔案

   ```sh
   vim hello.yml
   ```

   ```sh
   ---
   - hosts: server1
     remote_user: root
     
     tasks:
       - name: hello world
         command: /usr/bin/wall hello world
   ```

2. 執行腳本

   * `-C` 代表檢查

   ```sh
   ansible-playbook -C hello.yml
   ```

> 範例 2 : 自動產生網頁

1. 創建 `a.html` 網頁

   ```sh
   echo "Hi" > a.html
   ```

2. 編輯 `test.yml` 檔案

   ```sh
   vim test.yml
   ```

   ```sh
   ---
   - hosts: server1
     remote_user: root
     tasks:
       - name: create new file
         file: name=/tmp/newfile state=touch
       - name: create new user
         user: name=test2 system=yes shell=/sbin/nologin
       - name: install package # 安裝 httpd 套件
         yum: name=httpd
       - name: copy html # 將檔案複製到 app2 的指定資料夾
         copy: src=a.html dest=/var/www/html
       - name: start service # 啟動 httpd 服務
         service: name=httpd state=started
   ```

3. 執行腳本檔

   ```sh
   ansible-playbook test.yml
   ```

> 範例 3 : 忽略腳本中的錯誤

1. 編輯 `test3.yml` 檔案

   * 通常中間有錯就會停止執行腳本，就算後面程式是對的也不會繼續執行
   * 但在後方加入 ` || /bin/true` 可讓腳本就算出錯也能繼續執行

   ```sh
   vim test3.yml 
   ```

   ```sh
   ---
   - hosts: server1
     remote_user: root
     tasks:
       # 省略前方的 tasks
       - name: show date
         command: date
       - name: run a shell script
         shell: /usr/bin/somecommand || /bin/true
       - name: show hostname
         command: hostname
   ```

2. 執行腳本

   ```sh
   ansible-playbook -C test3.yml
   ```

> 範例 4 : Notify, Handler

* 由於 ansible 不論執行一次或多次結果都相同，會導致儘管修改了 httpd 的 Port 後，Port 仍然留在 80
* notify 後的 `restart httpd` 會觸發 handlers 中 name 為 `restart httpd` 的 service

1. 創建 `test4` 資料夾

   ```sh
   mkdir test4
   ```

2. 進入 `test4` 資料夾

   ```sh
   cd test4
   ```

3. 檢查 Client 端的 port

   ```sh
   ansible server1 -m shell -a "netstat -tunlp | grep httpd"
   ```

4. 編輯 Client 端的 `/etc/httpd/conf/httpd.conf`

   * Listen 80 改為 Listen 8080

   ```sh
   vim /etc/httpd/conf/httpd.conf
   ```

5. 編輯 `test4.yml` 檔案

   ```sh
   vim test4.yml
   ```

   ```sh
   # 錯誤範例 : 沒有 notify 無法重新啟動更改 httpd 的 port
   ---
   - hosts: server1
     remote_user: root
     tasks:
     # 編輯 copy html 的 task
       - name: copy html # 將檔案複製到 server1 的指定資料夾
         copy: src=/etc/httpd/conf/httpd.conf dest=/etc/httpd/conf
         name: start httpd
         service: name=httpd state=started
   ```

   ```sh
   # 正確範例
   ---
   - hosts: server1
     remote_user: root
     tasks:
     # 編輯 copy html 的 task
       - name: copy html # 將檔案複製到 app2 的指定資料夾
         copy: src=/etc/httpd/conf/httpd.conf dest=/etc/httpd/conf
         notify: restart httpd
         name: restart httpd
         service: name=httpd state=started
   ```

6. 確認 server1 上的 httpd 是否變為 8080 port

   ```sh
   ansible server1 -m shell -a "netstat -tunlp | grep httpd"
   ```

> 範例5 : parameter 透過 {{}} 包著

* parameter : 代表參數的符號
* argument : 代表實際的值

* 優先度 : 方法1 > 方法2 > 方法3

* 移除 `vsftpd` 套件

  ```sh
  rpm -e vsftpd
  ```

* 查看是否有 `vsftpd` 套件

  ```sh
  rpm -qa | grep vsftpd
  ```

* 方法一
  * 在腳本內設置參數，執行時直接設定參數

1. 編輯 `test5.yml` 檔案

   ```sh
   vim test5.yml
   ```

   ```sh
   ---
   - hosts: server1
     remote_user: root
     tasks:
     - name: install new package {{ pkgname }} # 顯示要安裝的套件
       yum: name={{ pkgname }}
   
     handlers:
       - name: restart httpd
         service: name=httpd state=started
   ```

2. 執行腳本，並將 `pkgname` 的參數設為 `vsftpd` 

   ```sh
   ansible-playbook -e pkgname=vsftpd test5.yml
   ```

* 方法二
  * 將參數的值寫在腳本內，直接執行腳本

1. 編輯 `test5.yml` 檔案

   ```sh
   vim test5.yml
   ```

   ```sh
   ---
   - hosts: server1
     remote_user: root
     vars:
      - pkgname1: vsftpd
      - pkgname2: httpd
     
     tasks:
      - name: install new package1 {{ pkgname1 }} # 顯示要安裝的套件
        yum: name={{ pkgname1 }}
      - name: install new package2 {{ pkgname2 }} # 顯示要安裝的套件
        yum: name={{ pkgname2 }}     
   
     handlers:
       - name: restart httpd
         service: name=httpd state=started
   ```

2. 執行腳本

   ```sh
   ansible-playbook -e test5.yml
   ```

* 方法三
  * 更改 `/etc/ansible/hosts` 的配置檔

1. 編輯 `/etc/ansible/hosts` 檔案

   ```sh
   vim /etc/ansible/hosts
   ```

   ```sh
   [servers]
   192.168.198.135 pkgname1=vsftpd pkgname2=vsftpd
   192.168.198.136 pkgname1=vsftpd pkgname2=vsftpd
   ```

2. 編輯 `test5.yml` 檔案

   ```sh
   vim test5.yml
   ```

   ```sh
   ---
   - hosts: server1
     remote_user: root
     vars:
   #   - pkgname1: vsftpd
   #   - pkgname2: httpd
     
     tasks:
      - name: install new package1 {{ pkgname1 }} # 顯示要安裝的套件
        yum: name={{ pkgname1 }}
      - name: install new package2 {{ pkgname2 }} # 顯示要安裝的套件
        yum: name={{ pkgname2 }}     
   
     handlers:
       - name: restart httpd
         service: name=httpd state=started
   ```

