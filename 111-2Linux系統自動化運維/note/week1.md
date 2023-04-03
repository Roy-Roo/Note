# Week 1



### IPV6



1. 在/var/www/html裡面創建webdav及hi.txt

   ```sh
   cd /var/www/html
   mkdir webdav
   cd webdav
   gedit hi.txt
   ```

2.  將電腦網路改成連手機的基地台

3.  對手機及電腦進行測試是否能使用ipv6

   * [測試](https://test-ipv6.com/index.html.zh_TW)
* 成功範例
     ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week1/week1-ipv6-1.jpg)

4. 設定linux電腦為橋接器

   * 只啟用一張介面卡(選擇橋接、WIFI)
     ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week1/week1-ipv6-2.jpg)

5. 開啟伺服器

   ```sh
   systemctl start httpd
   ```

6. 確認伺服器ipv6的ip

   ```sh
   ifconfig
   ```
   
   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week1/week1-ipv6-3.jpg)

7. 在瀏覽器上輸入ipv6的位置， 前後要加上[]， 範例 : 

   ```sh
   http://[2001:b400:e73f:1204:14b:9cf3:c85e:ce8d]/webdav/hi.txt
   ```

8. 由於位置太長，所以透過[dynv6](https://dynv6.com/zones)創辦帳號來使用ipv6的網域縮短位置

9. 兩種結果

   * 第一種
     ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week1/week1-ipv6-4.jpg)
   * 第二種![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week1/week1-ipv6-5.jpg)
     
