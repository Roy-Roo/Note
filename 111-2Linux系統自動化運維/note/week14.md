# Week14



### Linux高級檔案管理設定

* [參考](https://blog.gtwang.org/linux/how-to-make-file-immutable-on-linux-chattr-command/)

> Root 可刪除一般使用者無法刪除的檔案

1. 進到 tmp 資料夾下

   ```sh
   cd /tmp
   ```

2. 創建 testdir 資料夾

   ```sh
   mkdir testdir
   ```

3. 進到 testdir 資料夾下

   ```sh
   cd testdir
   ```

4. 創建 a,b,c,d 四個資料

   ```sh
   touch {a..d}
   ```

5. 查看 a,b,c,d的權限

   ```sh
   ls -l
   ```

6. 刪除檔案 d

   ```sh
   rm d
   ```

7. 查看剩餘檔案

   ```sh
   ls
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week14/Linux%E9%AB%98%E7%B4%9A%E6%AA%94%E6%A1%88%E7%AE%A1%E7%90%86%E8%A8%AD%E5%AE%9A-1.jpg)

8. 返回 tmp 資料夾

   ```sh
   cd ..
   ```

9. 更改檔案權限

   ```sh
   chmod u-w testdir/
   ```

10. 查看 testdir 資料夾的權限

    ```sh
    ls -ld testdir
    ```

11. 進到 testdir 資料夾下

    ```sh
    cd testdir
    ```

12. 刪除檔案 c 

    * 無法刪除

    ```sh
    rm c
    ```

    ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week14/Linux%E9%AB%98%E7%B4%9A%E6%AA%94%E6%A1%88%E7%AE%A1%E7%90%86%E8%A8%AD%E5%AE%9A-2.jpg)

13. 切換到 root

    ```sh
    su
    ```

14. 再次刪除檔案 c 

    * 可刪除

    ```sh
    rm c
    ```

    ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week14/Linux%E9%AB%98%E7%B4%9A%E6%AA%94%E6%A1%88%E7%AE%A1%E7%90%86%E8%A8%AD%E5%AE%9A-3.jpg)

* 新增高級屬性(連root都無法更動)

15. 查看高級屬性

    ```sh
    lsattr
    ```

16. 為檔案 a 新增 i 鎖

    * 檔案不可以被更動，不可以寫入、刪除、建立連結檔等

    ```sh
    chattr +i a
    ```

17. 編輯檔案 a

    * 無法寫入，`:q!` 退出 vim

    ```sh
    vim a
    ```

    ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week14/Linux%E9%AB%98%E7%B4%9A%E6%AA%94%E6%A1%88%E7%AE%A1%E7%90%86%E8%A8%AD%E5%AE%9A-4.jpg)

18. 刪除檔案 a

    * 無法刪除

    ```sh
    rm a
    ```

    ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week14/Linux%E9%AB%98%E7%B4%9A%E6%AA%94%E6%A1%88%E7%AE%A1%E7%90%86%E8%A8%AD%E5%AE%9A-5.jpg)

19. 將檔案 a 的 i 鎖移除

    ```sh
    chattr -i a
    ```

20. 為檔案 a 新增 a 鎖

    * 只能以附加的方式寫入

    ```sh
    chattr +a a
    ```

21. 編輯檔案 a

    * 無法使用 vim 進行寫入，只能用 echo 寫入
    * 通常用於 log 檔，用於阻止駭客刪除檔案的內容

    ```sh
    vim a
    ```

    ```sh
    echo "123" >> a
    ```

    ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week14/Linux%E9%AB%98%E7%B4%9A%E6%AA%94%E6%A1%88%E7%AE%A1%E7%90%86%E8%A8%AD%E5%AE%9A-6.jpg)

    ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week14/Linux%E9%AB%98%E7%B4%9A%E6%AA%94%E6%A1%88%E7%AE%A1%E7%90%86%E8%A8%AD%E5%AE%9A-7.jpg)

* 將 testdir 資料夾下的檔案全部加上 i 鎖

  * 參數 -R 可將資料夾下的檔案都一併處理

  ```sh
  chattr +i -R testdir
  ```



### Linux 細部權限 ACL

* [參考](https://ithelp.ithome.com.tw/articles/10221185)

* 判斷系統內有沒有名為 mary 的使用者

  ```sh
  id mary
  ```

  * 寫在腳本內

  ```sh
  id mary > /dev/null 2>&1
  echo $?
  ```

1. 創建使用者 mary

   ```sh
   useradd mary
   # password : user
   ```

2. 創建使用者 tom

   ```sh
   useradd tom
   # password : user
   ```

3. 取得檔案 a 的存取控制清單

   ```sh
   getfacl a
   ```

4. 設定權限

   * 讓 mary 對 a 有可讀可寫的權限

   ```sh
   setfacl -m u:mary:rw a
   ```

5. 切換使用者為 mary

   ```sh
   su mary
   ```

6. 編輯檔案 a

   * 可編輯

   ```sh
   echo "123" >> a
   ```

7. 切換使用者為 tom

   ```sh
   su tom
   ```

8. 編輯檔案 a

   * 無法編輯

   ```sh
   echo "321" >> a
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week14/Linux%E9%AB%98%E7%B4%9A%E6%AA%94%E6%A1%88%E7%AE%A1%E7%90%86%E8%A8%AD%E5%AE%9A-8.jpg)

* 針對特定群組

  * 參數 -m

  ```sh
  setfacl -m g:groooop:wx test
  ```

* 有效權限遮罩：m (mask)

  * 代表權限最多只能使用 mask 所列出來的，若是多開會被過濾掉無法使用該權限



### Ansible

* 基於 SSH 來做維護和管理

1. 分別設定3台虛擬機的 hostname 為centos7-1、centos7-2、centos7-3

   ```sh
   hostnamectl set-hostname centos7-1
   bash
   ```

2. 生成金鑰(公私鑰)

   ```sh
   ssh-keygen
   ```

3. 查看各虛擬機的 IP

   ```sh
   ifconfig
   ```

4. 編輯 /etc/hosts 檔案

   ```sh
   vim /etc/hosts
   ```

   ```sh
   192.168.198.137 centos7-1
   192.168.198.135 centos7-2
   192.168.198.136 centos7-3
   ```

5. 將本地用戶的公鑰複製到遠程主機上的指定用戶帳號中

   * 另外兩台虛擬機

   ```sh
   ssh-copy-id root@centos7-2
   ssh-copy-id root@centos7-3
   ```

6. 遠端登入 centos7-2、centos7-3

   ```sh
   ssh root@centos7-2
   ssh root@centos7-3
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week14/Ansible-1.jpg)

7. 安裝 ansible

   ```sh
   yum install ansible -y
   ```

8. 編輯 /etc/ansible/hosts 檔案

   ```sh
   vim /etc/ansible/hosts
   ```

   ```sh
   [server1]
   192.168.198.135
   
   [server2]
   192.168.198.136
   
   [servers]
   192.168.198.135
   192.168.198.136
   ```

9. 檢查 client 端是否有回應

   * 要顯示 `"ping" : pong ` 才算成功

   ```sh
   ansible servers -m ping
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week14/Ansible-2.jpg)

10. 列出 ansible 有支援的模組

    ```sh
    ansible-doc -l
    ```

    * mysql 也可替換成 mac、windows 之類的來進行搜尋

    ```sh
    ansible-doc -l | grep -i musql
    ```

11. 指定 server1 執行 ifconfig 的指令並回傳結果

    * cammand 只能執行簡單的指令
    * 並不是所有指令都能用cammand，例如 > 、 | 就沒辦法使用 command

    ```sh
    ansible server1 -m command -a "ifconfig"
    ```

    * 也可以指定其他server或執行其他指令

    ```sh
    ansible servers -m command -a "ifconfig ens33"
    ```

12. 執行 Shell指令

    * 可執行較複雜的指令
    * 可使用 | 功能

    ```sh
    ansible servers -m shell -a "ifconfig ens33 | grep inet"
    ```

13. 查看 Shell 指令的用法

    ```sh
    ansible-doc -s shell
    ```

14. 查詢當前資料夾 pwd

    ```sh
    ansible servers -m shell -a "pwd"
    ```

15. 切換到 tmp 資料夾 

    ```sh
    ansible servers -m shell -a "chdir=/tmp pwd"
    ```

16. 檢查檔案 `/tmp/a` 是否存在，如果存在則執行 `ls -l` 命令

    ```sh
    ansible servers -m shell -a "creates=/tmp/a ls -l"
    ```

17. 在 `/var/log` 資料夾中執行命令 `ls -l | grep log` 

    ```sh
    ansible servers -m shell -a "chdir=/var/log cmd='ls -l | grep log'"
    ```

    
