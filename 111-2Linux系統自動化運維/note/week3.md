# Week 3 (第二週禮拜一晚上的補課)



### 建立憑證並且連上https

1.  確認已經順利連上IPV6，並找到其位置

2.  在[dynv6](https://dynv6.com/zones/3393973/records)上申請網域

3.  開啟httpd測試是否可以用IPV6

   ```sh
   systemctl start httpd
   ```

4. 安裝 epel-release mod_ssl certbot

   ```sh
   yum install epel-release mod_ssl certbot
   ```

5.   申請IPV6的憑證

   ```sh
   certbot certonly --webroot -w /var/www/html -d royroy123.dynv6.net --email s110910519@student.nqu.edu.tw --agree-tos
   ```

   ```sh
   certbot --apache -d roy123.dynv6.net
   ```
   
   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week3/https-1.jpg)

6. 成功後的網站可用https連線而不會出現不安全

   ```sh
   https://roy123.dynv6.net/webdav/hi.txt
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week3/https-2.jpg)



***補充***

若是遇到以下問題的解決方法
![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week3/https-3.jpg)



1. 找到httpd.conf文件並修改

   ```sh
   cd /etc/httpd/conf
   ```

   ```sh
   vim httpd.conf
   ```

   在<Listen 80>處新增以下配置

   ```sh
   Listen 80
   
   <VirtualHost *:80>   
       ServerAdmin test@test.example.com 
       ServerName www.test.com 
       ServerAlias test 
       DocumentRoot /var/www/html 
   </VirtualHost>
   ```

2. 重新啟動httpd

   ```sh
   systemctl restart httpd
   ```

   
