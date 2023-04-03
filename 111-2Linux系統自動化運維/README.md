# 學習重點

| 欄位       | 內容                                                         |
| ---------- | ------------------------------------------------------------ |
| [第一週](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/week1.md#week-1)     | [IPV6](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/week1.md#ipv6)                                                       |
| 第二週     | 19                                                           |
| 第三週     | [金門大學資訊工程系](https://www.nqu.edu.tw/educsie/index.php) |
| 第四週     | [金門大學資訊工程系](https://www.nqu.edu.tw/educsie/index.php) |
| 第五週     | [金門大學資訊工程系](https://www.nqu.edu.tw/educsie/index.php) |
| 第六週     | [金門大學資訊工程系](https://www.nqu.edu.tw/educsie/index.php) |
| 第七週     | [金門大學資訊工程系](https://www.nqu.edu.tw/educsie/index.php) |
| 第八週     | [金門大學資訊工程系](https://www.nqu.edu.tw/educsie/index.php) |
| 第九週     | [金門大學資訊工程系](https://www.nqu.edu.tw/educsie/index.php) |
| 第十週     | [金門大學資訊工程系](https://www.nqu.edu.tw/educsie/index.php) |
| 第十一週     | [金門大學資訊工程系](https://www.nqu.edu.tw/educsie/index.php) |
| 第十二週     | [金門大學資訊工程系](https://www.nqu.edu.tw/educsie/index.php) |
| 第十三週     | [金門大學資訊工程系](https://www.nqu.edu.tw/educsie/index.php) |
| 第十四週     | [金門大學資訊工程系](https://www.nqu.edu.tw/educsie/index.php) |
| 第十五週     | [金門大學資訊工程系](https://www.nqu.edu.tw/educsie/index.php) |

# 注意事項



### 重啟 VMware 的 NAT



1. 打開 Windows 的服務管理器

   ```
   Windows + R
   ```

   ```
   services.msc
   ```

2. 尋找 `VMware NAT Service` 服務。



### Enforce 關閉



1.  打開配置檔

   ```sh
   gedit /etc/selinux/config
   ```

   ```sh
   --> SELINUX=disabled
   ```

2. 重啟虛擬機

   ```sh
   reboot
   ```

3. 將 enforce 設為0

   ```sh
   setenforce 0 
   ```

4. 檢查 enforce 是否為 `disable` 或 `permissive` 

   ```sh
   getenforce



### Firewalld 關閉



1. 系統將不會在下次開機時啟動 firewalld 服務

    ```sh
    systemctl disable firewalld
    ```

2.  系統將立即停止 firewalld 服務的運行

   ```sh
   systemctl stop firewalld
   ```

3. 查看 firewalld 服務的運行狀態

   ```sh
   systemctl status firewalld
   ```

   

