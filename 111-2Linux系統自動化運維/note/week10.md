# Week10



### JumpServer

* User(一般使用者)

  * 創建新使用者

    ```
    Name : Mary             # 可以不用跟虛擬機上的名字一樣
    Username : Mary
    Email : Mary@abc.com
    
    Password : 123456      # 要6位數，Update password next time 要取消勾選
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

  * create-common user

    ```
    Name : user        # 跟虛擬機上的Username一樣
    Protocol : SSH
    Username : user
    passward : user
    ```

* Asset permissions

  * create

    ```
    Name : MaryForCentos7-2
    User : Mary
    user group : Default
    Asset : centos7-2        # 要打勾進行綁定
    Nodes : Default
    System user : user
    ```


> 換成Mary登入

* Web Terminal

  * centos7-2

    ```
    Web Cll
    ```

    ![](https://github.com/Roy-Roo/Note/tree/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week10/jumpserver-1.jpg)

> 換回 admin登入

* 查看 Mary 做過的事

  ```
  點選 View -> 改為 Audit
  ```

  ```
  選擇 Sessions audit -> Commands
  ```

  ```
  選擇 Goto -> 右邊即可點擊 replay 查看重播
  ```

  ![](https://github.com/Roy-Roo/Note/tree/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week10/jumpserver-2.jpg)

* Command Filters

  ```
  Console -> Assets -> Command Filters
  Name : ForMary
  User : Mary
  Nodes : Default
  Asset : centos7-2
  System user : user
  ```

  ```
  點選 ForMary
  Command Filter rules -> create
  ```

  ```
  content : rm
  		  reboot
  Action : Deny
  ```


> JumpServer 管理 Win7

1. 先在Win7內的cmd中確認IP(IPv4)

   ```
   ipconfig
   ```

2. 控制台 -> 系統安全性 -> 系統 -> 系統內容 -> 遠端桌面 -> 允許來自任何版本遠端桌面連線

3. 控制台 -> 系統安全性 -> Windows防火牆 -> 關閉防火牆

4. 在本地端啟動遠端桌面連線 -> 輸入IP

   ```
   帳號 : smallko
   密碼 : smallko
   ```

5. 使用 admin 登入 JumpServer 

6. Console -> Assets -> Create

   ```
   Hostname : win7
   IP : 192.168.153.130  # 要改用自己的win7的IP
   Platform : Windows
   Protocols : rdp 3389
   Nodes : Default
   ```

7. Console -> System Users -> Common user -> Create

   ```
   RDP
   Name : smallko
   Protocol : RDP
   Username : smallko
   login mode : Automatic managed
   Password : smallko
   ```

8. Console -> Permissions -> Asset Permissions -> Create

   ```
   Name : MaryForWin7
   User : mary
   Asset : win7
   Nodes : Default
   System user : smallko
   ```

9. 換成 mary 登入

10. web terminal -> win7

    
