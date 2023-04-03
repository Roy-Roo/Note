# Week 2



### 架設WebDAV

* 如果網路空間很大，就可以把網路當作磁碟機，當作自己的雲端硬碟，可以建立備份
* 在本地端可以使用 http，在雲端上在使用 https 架設安全性比較高
* 這個可以取代 samba (漏洞較多，易被攻擊)，開發嵌入式平台時用此方式能在 windows 上操作



**如果是用https，那下面就會報錯**

1. 安裝第3方資料庫 epel-release

   ```sh
   yum install epel-release
   ```

2. 安裝網站伺服器 httpd

   ```sh
   yum install httpd
   ```

3. 檢查http是否有支援Webdav

   ```sh
   httpd -M | grep dav
   ```

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week2/webdav-1.jpg)

4. 到 html 資料夾下

   ```sh
   cd /var/www/html
   ```

5. 創建 webdav 資料夾

   ```sh
   mkdir webdav
   ```

6. 修改擁有者及使用者權限

   ```sh
   chown -R apache:apache /var/www/html
   ```

   ```sh
   chmod -R 755 /var/www/html 
   ```

7. 編輯配置檔

   ```sh
   vim /etc/httpd/conf.d/webdav.conf
   ```

   ```sh
   DavLockDB /var/www/html/DavLock
   <VirtualHost *:80>
       ServerAdmin webmaster@localhost
       DocumentRoot /var/www/html/webdav/
       ErrorLog /var/log/httpd/error.log
       CustomLog /var/log/httpd/access.log combined
       Alias /webdav /var/www/html/webdav
       <Directory /var/www/html/webdav>
           DAV On
           #AuthType Basic
           #AuthName "webdav"
           #AuthUserFile /etc/httpd/.htpasswd
           #Require valid-user
           </Directory>
   </VirtualHost>
   ```

8. 啟動httpd

   ```sh
   systemctl start httpd
   ```

9. 打開windows的網路磁碟機，連上linux的ip位置，輸入http:\192.168.160.226即可找到

   ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week2/webdav-2.jpg)



### Sed (stream editor)



* Linux三劍客  `awk` / `grep` / `sed`

* 寫腳本的時候很常會用到



> 範例

```sh
cat <<EOF >settings.conf  # 把下面的內容放到settings.conf，直到打出EOF
> name: Alan
> age: 18
> EOF
```

* 腳本執行範例 1

```sh
vim gen.sh
```

```sh
echo "generate a.conf"

cat <<EOF >a.conf
name: Alan
age: 18
EOF
```

```sh
bash gen.sh
```

* 腳本執行範例 2

```sh
vim test.sh
```

```sh
#read -p : 可以在螢幕上顯示" "內的字，並把使用者輸入紀錄到後面的變數中
read -p "what's your name?" name
read -p "what's your age?" age

cat <<EOF >temp.sh
echo "hi " $name
echo "your age is: " $age
EOF

/usr/bin/bash temp.sh
```

```sh
bash test.sh
what's your name?Tom
what's your age?20
hi Tom
your age is: 20
```

* 腳本執行範例 3

```sh
# 使用者輸入y或Y開頭才能繼續的腳本
# case開始、esac結束 (if也是同樣的使用方法if開始、fi結束)
# $anwer 使用變數前面要加上 $
# [Yy]* ) 代表輸入Y或是y就可以繼續
# * 在正則表達式中代表重複前面的字0次或任意次
# * ) 等同於 C 語言中的 default，輸入其他的就直接離開
read -p "Do you want to continue? (Y/y for Yes, any other key for No)" answer
    case $anwer in
        [Yy]* ) echo "Program continues..."; break;;
        * ) echo "Program exits."; exit;;
    esac
```



> 常用sed做取代的功能

* s : substitute(取代)
* g : globally(全部)
* `sed 's/A/B/g'` : 將A全部取代為B，如果不加g就是取代第一個，中間不一定要使用/，也可以用其他符號(例如#)，只要是三個就好了

```sh
# 使用時最好先寫出///，再將要取代的字串分別填入///中
cat setting.conf
name: Alan
age: 18
sed 's/Alan/Tom/' setting.conf  # 只修改顯示的檔案內容，沒有改到原始檔案
sed -i 's/Alan/Tom/' setting.conf  # 修改原始檔案，且顯示修改後的檔案內容
```



### 正規表達式

| 特殊符號 | 解釋                                         |
| :------- | :------------------------------------------- |
| .        | 代表除了\n以外的任意的字元                   |
| ^...     | 匹配以...為開頭                              |
| ...$     | 匹配以...為結尾                              |
| *        | 重複前面的字0次或任意次                      |
| \        | 將下一个字符標記或下一個為特殊符號(例如空白) |
| [ ]      | 代表匹配其中的任何一個字符                   |

* ^$ : 代表空白行

* d : delet(刪除)，代表匹配到的就刪掉
* .* : 從第一個到最後
* & : 代表匹配到的所有內容



> 範例

* 刪除空白行

```sh
sed -i 's/^$/d/' setting.conf 
```

* 字串抓取

```sh
echo "1234abc" | grep ".*" # 代表匹配全部
```

```sh
echo "1234abc" | grep "3.*" # 代表從3開始到結束
```

* 取代

```sh
cat a.conf
name: Tom
age: 18
```

```sh
sed 's/name:.*/name: Duke/g' a.conf #從.*前面的字串到結束
name: Duke
age: 18
```

* \ 的用法

```sh
cat bb
abc d e123
```

```sh
sed 's/abc\ d/a\ bcd/' bb # 空白是特殊符號，不能直接寫，前面要加上\
a bcd e123
```

```sh
sed 's#abc\ d#a\ bcd#' bb # /\一起用會看不清楚，可將/改成#來使用較好區分
a bcd e123
```

* 取代

```sh
echo "http://www.a.com" | sed "s#^http#https#"
https://www.a.com
```

```sh
echo "http://www.a.com.tw" | sed "s#^com.*#edu#"
http://www.a.edu
```

```sh
# 將上面兩種合併
echo "http://www.a.com" | sed "s#^http#https#" | sed "s#^com.*#edu#"
https://www.a.edu
```

* 去除空白

```sh
cat bb
  aa
aa
    bb
bb
	  c
cc
```

```sh
# [ \t] 代表匹配其中任何一個字，這邊匹配空白和tab
sed 's#^[ \t]*# #' bb
 aa
 aa
 bb
 bb
 c
 cc
```

```sh
sed 's#^[ \t]*##' bb
aa
aa
bb
bb
c
cc
```

* 1234前後加上()

```sh
echo "1234" | sed 's/.*/(&)/'
(#)
echo "1234" | sed 's/.*/(#&)/'  
(1234)
echo "1234" | sed 's/.*/(&)/'  # & 代表匹配到的所有內容
(1234)
```

* 

```sh
echo "abccddddfgdajf" | sed 's/.*/#&/' # 可以把配置檔內容加上註解
#abccddddfgdajf
```

