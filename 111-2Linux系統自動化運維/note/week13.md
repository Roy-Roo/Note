# Week13



### Zabbix LINE Notify

* [參考](https://dotblogs.com.tw/xerion30476/2019/08/28/153643)

1. 開啟 https://notify-bot.line.me/zh_TW/ ，登入帳號，右上角的ID -> 個人頁面 -> 發行權杖 -> 透過1對1聊天接收LINE Notify的通知，取得權杖後要記錄下來，出現後便不再顯示

   ```
   uD0vOlwoDzyW6H5cxO3PfnhzcRoHUt9sfqjKjgpKTq6
   ```

2. 設定Scritp

   ```sh
   cd /usr/lib/zabbix/alertscripts/
   ```

   ```sh
   vim line_notify.sh
   ```

   ```sh
   #!/bin/bash
   # LINE Notify Token - Media > "Send to".
   TOKEN="$1"
   
   # {ALERT.SUBJECT}
   # subject="$1"
   
   # {ALERT.MESSAGE}
   message="$1"
   
   curl https://notify-api.line.me/api/notify -H "Authorization: Bearer ${TOKEN}" -F "message=${message}"
   ```

3. 設定權限

   ```sh
   chmod +x ./line_notify.sh
   ```

   ```sh
   chown zabbix:zabbix line_notify.sh
   ```

4. 測試

   ```sh
   ./line_notify.sh test "Sent from my Centos7"
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week13/Zabbix LINE Notify-1.jpg)

* Zabbix Ui 設定

5. Administration -> Media types -> Create media type

   * Media type

   ```
   Name : Line Notify
   Type : Script
   Script name : line_notify.sh
   Script parameters : {ALERT.SENDTO} 
   					{ALERT.SUBJECT} 												{ALERT.MESSAGE}
   ```

   * Message templates

   ```
   Problem
   Problem recovery
   ```

6. Administration -> Users -> Admin -> Media -> Add

   * 以下設定完成要記得按update

   ```
   Type : Line Notify
   Send to : XXX          # 第一步取得的權杖
   ```

7. Configuration -> Actions -> Create action

   * Action

   ```
   Name : LineNotify
   Conditions : Trigger severity is greater than or equals Warning
   ```

   * Operations

   ```
   Operation details : 
   	Send to user groups : Zabbix administrators
   	Send only to : Line Notify
   Operation details :
   	Send to user groups : Zabbix administrators
   	Send only to : Line Notify 
   ```

   ```
   # Operation details(Custom message)
   	Subject : {HOST.NAME1}：{TRIGGER.STATUS}：{TRIGGER.NAME}
   	Message : 主機名稱: {HOSTNAME1}
   			  發生時間: {EVENT.DATE} {EVENT.TIME}
   			  警示等級: {TRIGGER.SEVERITY}
   			  警示訊息: {TRIGGER.NAME}
   			  警示項目: {TRIGGER.KEY1}
   			  問題說明: {ITEM.NAME}: {ITEM.VALUE}
   			  當前狀態: {TRIGGER.STATUS}: {ITEM.VALUE1}
   			  事件ID: {EVENT.ID}
   ```

8. 到 Client 端測試

   ```sh
   cat /dev/urandom | md5sum
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week13/Zabbix LINE Notify-2.jpg)



### Zabbix 無 surport 時的監控作法

* 在客戶端執行

1. 切換到 zabbix 資料夾

   ```sh
   cd /etc/zabbix
   ```

2. 編輯 zabbix_agent.conf 檔

   ```sh
   vim zabbix_agentd.conf
   ```

   ```sh
   # 有無開啟httpd
   # 第343行
   UserParameter=test_param,curl 127.0.0.1> /dev/null 2>&1; if [ "$?" -eq "0" ]; then echo "1"; else echo "0"; fi 
   ```

3. 重啟 zabbix-agent.service

   ```sh
   systemctl restart zabbix-agent.service
   ```

4. 在 Server 端測試

   ```sh
   zabbix_get -s 192.168.198.136 -p 10050 -k "test_param"
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week13/Zabbix-1.jpg)

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week13/Zabbix-2.jpg)

> 查看有多少個連線到這台設備

* 此方法會在顯示時多1，是因為多了 Server 端詢問時的連線

* 在客戶端執行

1. 切換到 zabbix 資料夾

   ```sh
   cd /etc/zabbix
   ```

2. 編輯 zabbix_agent.conf 檔

   ```sh
   vim zabbix_agentd.conf
   ```

   ```sh
   # 第343行
   UserParameter=connections,netstat -an | grep "ESTABLISHED" | wc -l
   ```

3. 重啟 zabbix-agent.service

   ```sh
   systemctl restart zabbix-agent.service
   ```

4. 在 Server 端測試

   ```sh
   zabbix_get -s 192.168.198.136 -p 10050 -k "connections"
   ```

> 在 Zabbix 網頁新增控制項

1. Configuration -> Hosts -> 要新增控制項的主機的 items 欄位 -> Create item

   ```
   Name : httpd_isrunning
   Key : test_param
   Type of information : Numeric (unsigned)
   Update interval : 1s
   Applications : General
   ```

2. Monitoring -> Hosts -> Centos7-3 -> Latest data -> General 的  httpd_isrunning 項的 Graph

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week13/Zabbix-3.jpg)

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week13/Zabbix-4.jpg)



### 題目



* 監控 SSH 伺服器。如果已建立的 SSH 連線數超過 3 個，Zabbix 伺服器將會發送 Line Notify 或電子郵件通知，告知連線數量過大。

  * 在客戶端執行

  1. 切換到 zabbix 資料夾

     ```sh
     cd /etc/zabbix
     ```

  2. 編輯 zabbix_agent.conf 檔

     ```sh
     vim zabbix_agentd.conf
     ```

     ```sh
     # 第343行
     UserParameter=ssh_connections,netstat -an | grep 22 | grep ESTABLISHED | wc -l
     ```

  3. 重啟 zabbix-agent.service

     ```sh
     systemctl restart zabbix-agent.service
     ```

  * 在 Zabbix 網頁新增控制項

  4. Configuration -> Hosts -> 要新增控制項的主機的 items 欄位 -> Create item

     ```
     Name : SSH_connections
     Key : ssh_connections
     Type of information : Numeric (unsigned)
     Update interval : 1s
     Applications : General
     ```

  5. Configuration -> Hosts -> 要新增控制項的主機的 Triggers 欄位 -> Create trigger

     ```
     Name : SSH_connections > 3
     Expression : {Centos7-3:ssh_connections.last()}>3
     ```

     ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week13/Zabbix-5.jpg)

