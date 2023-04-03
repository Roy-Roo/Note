# 注意事項



### 重啟NAT



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

   

