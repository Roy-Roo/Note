# Week12



### Zabbix

> Applications

* 為大分類
* 有分成CPU、磁碟、檔案系統、網路卡、儲存等等，共17個項目

> Item

* Applications底下的細項
* 舉例 : CPU interrupt time、CPU guest time等等
* 若是別人有做好的就能用，若是沒有可以自己再加

> Triggers

* 設定如果有發生嚴重的事來觸發並通知
* 舉例 : CPU 過載、磁碟空間不足
* 發送 Email 或 Line 來通知

> 查看監控項

* Monitoring -> Hosts -> 確認要查看的主機的 Graphs 欄位

> 手動配置監控的 items 用 Triggers 偵測，並用 Gmail 發送警告訊息

1. 先將原本有 Templates 的 Host 刪除，重新生成一個沒有 Templates 的 Host 

2. Configuration -> Hosts -> 要監控主機的 Items 欄位 -> create item

   * 在生產線上 Update interval 最好設置30秒或1分鐘，不要設1秒鐘

   ```
   Name : CPU utilization rate
   Key : system.cpu.util[,idle]
   Type of information : Numeric(float)
   Units : %
   Update interval(回傳的頻率) : 1s
   ```

   * CPU utilization rate -> Preprocessing 

     ```
     Name : JavaScript
     Parameters : return 100 - value
     ```

   ```sh
   # 手動查詢監控項的 key(idle)
   zabbix_get -s 192.168.198.133 -p 10050 -k "system.cpu.util[,idle]"
   ```

3. 點選 CPU utilization rate -> test -> Get value
   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week12/zabbix-1.jpg)

4. Configuration -> Hosts -> 要監控主機的 Triggers 欄位 -> Create trigger

   ```
   Name : CPU utilization rate > 5% in one minute
   Severity : Warning
   Expression : {Centos7-2:system.cpu.util[,idle].avg(1m)}>=5
   ```

   * Expression -> Add

     ```
     Item : Centos7-2: CPU utilization rate
     Function : avg() - Average value of a period T
     Last of (T) : 1m
     Result : >= 5
     ```

5. 測試

   * 在 client 端使用

   ```sh
   cat /dev/urandom | md5sum
   ```

   * 在 Monitoring -> Dashboard

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week12/zabbix-2.jpg)

   * 在 Monitoring -> Latest data -> Centos7-2的 Graph

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week12/zabbix-3.jpg)

* 以下在 Server 端做

6. 先到 alertscripts 資料夾下

   ```sh
   cd /usr/lib/zabbix/alertscripts/
   ```

7. 編輯腳本

   ```sh
   vim mailx.sh
   ```

   ```sh
   #!/usr/bin/bash
   #send mail
   messages=`echo $3 | tr '\r\n' '\n'`
   subject=`echo $2  | tr '\r\n' '\n'`
   echo "${messages}"| mail -s "${subject}" $1 2>&1
   ```

8. 給予權限

   ```sh
   chmod +x mailx.sh
   ```

9. 測試

   ```sh
   ./mailx.sh s110910519@student.nqu.edu.tw Test "Hello World"
   ```

10. 給予權限

    ```sh
    chown -R zabbix:zabbix /usr/lib/zabbix
    ```

* 以下在 Zabbix 網頁做

11. Administration -> Media types -> Create media type

    ```
    Name : Gmail
    Type : Script
    Script name : mailx.sh
    Script parameters : {ALERT.SENDTO}
    	                {ALERT.SUBJECT}
    	                {ALERT.MESSAGE}
    ```

12. Administration -> Media types -> Gmail 的 test

    ```
    Send to : s110910519@student.nqu.edu.tw
    ```

13. Administration -> Media types -> Gmail -> Message templates -> Add(Problem 、 Problem recovery) -> Update

14. Administration -> Users -> Admin -> Media -> Add

    ```
    Type : Gmail
    Send to : s110910519@student.nqu.edu.tw
    When active : 1-7,00:00-24:00
    ```

15. Configuration -> Actions -> Create action

    ```
    Name : Gmail Notify action
    Conditions : A	Trigger severity is greater than or equals Warning
    ```

    * Conditions

      ```
      Type : Trigger severity
      Operator : is greater than or equals
      Severity : Warning
      ```

    * Operations

    ```
    Operation details : 
    	Send to users : Admin (Zabbix Administrator)
    	Send only to : Gmail
    Recovery operations : 
    	Send to users : Admin (Zabbix Administrator)
    	Send only to : Gmail
    ```

16. 回到 Cilent 端測試

    ```sh
    cat /dev/urandom | md5sum
    ```

17. 到信箱查看是否有寄信
    ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week12/zabbix-4.jpg)

18. 回到 Cilent 端使用 Ctrl + C 停止測試再回信箱查看

    ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week12/zabbix-5.jpg)

    
