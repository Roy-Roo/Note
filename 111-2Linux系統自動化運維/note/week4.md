# Week 4



### OpenAI Translator，Chrome的翻譯擴充功能

> [步驟](https://www.kocpc.com.tw/archives/482936)

1.  下載 ***openai-translator-chrome-extension*** 並解壓縮[(載點)](https://github.com/yetone/openai-translator/releases/download/v0.0.15/openai-translator-chrome-extension-0.0.15.zip)
2.  開啟 ***Chrome*** -> ***設定*** -> ***擴充功能*** -> ***開發人員模式開啟***  -> ***載入未封裝項目***  -> ***選擇解壓縮的資料夾***
3.  點選 **Chrome** 右上角的擴充功能圖示將 ***OpenAI Translator*** 固定並開啟![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week4/OpenAI%20Translator-1.jpg)
4.  開啟 ***OpenAI*** 的 [API keys](https://platform.openai.com/account/api-keys)
5.  點選 ***Create new secert key***
6.  將新增的 API key 複製貼上到 ***Chrome*** 右上角的擴充功能的 ***OpenAI Translator***  中



### ChatGPT整合到Line Bot

> [步驟](https://mrmad.com.tw/chatgpt-line-robot-creation-teaching)

1.  註冊 LINE 免費開發者帳號，[LINE Developers LINE 開發者平台](https://developers.line.biz/zh-hant/) ，使用 LINE 帳號登入即可

2.  選擇 ***Create a Messaging API channel*** ![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week4/ChatGPT%E6%95%B4%E5%90%88%E5%88%B0Line%20Bot-1.jpg)

3.  在創建頻道頁面（Create a new channel）依照底下提示輸入：

   ```
   Channel type（頻道）：Messaging API
   Provider（類型）：My Provider
   Company or owner’s country or region（所在國家）：填入目前居住國家台灣直接選 Taiwan
   Channel icon（頻道圖示）：點我下載 ChatGPT 圖示
   Channel name（頻道名字）：ChatGPT AI Assistant （先輸入後續可修改）
   Channel description：ChatGPT
   Category：隨便選
   Subcategory：隨便選
   Email address：預設 LINE Email 不用改
   Privacy policy URL：免填寫
   ```

   最後將同意 LINE 聲明條款都打勾，按下「Create」繼續

4.  LINE 機器人頻道頁面中，切換到「Messaging API」，找到 Channel access token 設定，點擊「Issue」按鈕，產生一組 LINE Channel access token，將其複製並記錄在記事本上

5.  開啟 ***OpenAI*** 的 [API keys](https://platform.openai.com/account/api-keys)，點選 ***Create new secert key*** ，將新增的 API key 複製並記錄在記事本上

6. LINE 機器人頻道頁面中，切換到「**Basic settings**」，往下滑找到 **Channel secret** ，將 Channel secret 後面那串值複製起來並記錄在記事本上。

7.  開啟由台灣網友 @memochou1993 撰寫的 GPT 人工智能助手[Github 原始專案](https://github.com/memochou1993/gpt-ai-assistant)（OpenAI + LINE + Vercel），直接點擊「Fork」按鈕，就能將專案分流到自己 Github 帳號底下

8.  開啟 [Vercel 平台](https://vercel.com/dashboard)後，點選「Start Deploying」按鈕開始，點選「**Continue with GitHub**」透過 Github 來註冊帳號，點選「**Authorize vercel**」繼續，申請過程需要**電話號碼收驗證簡訊**，台灣直接選 +886 並輸入電話號碼（免輸入0），點擊「**Continue**」按鈕繼續，過程會出現 **Installing Vercel 視窗**，內部不用修改，直接滑到最底點選「**Install**」按鈕繼續。

9.  點選「**Create a New Project**」（創建新項目）來**建立一個新專案** ，直接點「**Import**」按鈕，就能將 Github 存儲庫內的 gpt-ai-assistant 專案導入到平台上，接下來需要設定專案，直接點開「**Environment Variables**」（環境變量）頁簽，依照底下提示輸入環境變化數，最後按下「**Add**」新增，按下「**Deploy**」開始部署。

   ```
   【第一組 OpenAI Keys 設定】
   Name：OPENAI_API_KEY
   Value：值設置貼上 OpenAI 網站產生的 ChatGPT Keys
   
   【第二組 LINE channel access token 設定】
   Name：LINE_CHANNEL_ACCESS_TOKEN
   Value：將值設置為 LINE 頻道 channel access token
   
   【第三組 設定】
   Name：LINE_CHANNEL_SECRET
   Value：將值設置為 LINE 頻道 channel secret
   ```

10. 點擊右上角「**Continue to Dashboard**」進入儀表板設定，直接點入第一個 **DEPLOYMENT 連結**，複製**應用程式網址**（Domains），例如 ***https://gpt-rust.vercel.app***

11. 回到 LINE 頻道頁面，切換到「**Messaging API**」分頁，找到 **Webhook settings 的 Webhook URL 設定**，點擊「**Edit**」按鈕，直接貼上「https://gpt-rust.vercel.app」，提醒網址最後加入「/webhook」路徑，完整 Webhook URL 路徑為「https://gpt-rust.vercel.app/webhook」，最後點「**Update**」按鈕套用

12. 完成 Webhook 網址設定後，點一下「**Verify**」檢查是否能呼叫成功，只有 **Success 代表網址輸入正確**，要是顯示其他錯誤代表你複製的網址或沒有將路徑填寫完整

13. 往下將「**Use webhook**」功能開啟，代表 **LINE 頻道允許使用 webhook** ，往下找到 **LINE Officaial Account features 設定**，點擊右側「**Edit**」編輯文字，會自動開啟另一個回應功能設定，直接將**「聊天、加入好友的歡迎訊息、自動回應訊息」功能關閉**，**唯獨「Webhook」功能開啟**即可

14. 以上設定完成後，不需要按更新或確認，**直接重新整理 LINE 開發者頻道網頁**，**會看見三個選項全改為「Disabled」關閉狀態**。

15. 從 LINE 開發者頁面中切換成 Messaging API 選項，底下會**顯示手動創建的 ChatGPT LINE 機器人 LINE@帳號與 QR Code** ，**直接用手機版 LINE App 掃描 QR Code 就能加入 ChatGPT LINE@ 帳號**。



### 為影片自動上字幕

1. 安裝 Miniconda [載點](https://docs.conda.io/en/latest/miniconda.html#linux-installers)

   ```sh
   wget https://repo.anaconda.com/miniconda/Miniconda3-py310_23.1.0-1-Linux-x86_64.sh
   ```

2.  執行 Miniconda

   ```sh
   bash Miniconda3-py310_23.1.0-1-Linux-x86_64.sh
   ```

   一直按 Enter ，選擇 yes ， 再按一次 Enter ， 選擇 no，即可安裝好

3.  執行以下指令

   ```sh
   /root/miniconda3/bin/conda config --set auto_activate_base false
   ```

4.  執行以下指令

   ```sh
   vim .bashrc
   ```

   在 fi 下面加上

   ```sh
   export PATH=$PATH:/root/miniconda3/bin
   ```

5. 執行以下指令，在 Linux 創建 python 虛擬環境

   ```sh
   source .bashrc
   ```

   ```sh
   conda init
   ```

6. 離開重新進入

   ```sh
   exit
   ```

   ```sh
   su
   ```

7. 創建一個名為 mypython3.10 的新環境，python 版本為3.10

   ```sh
   conda create -n mypython3.10 python=3.10
   ```

8. 開啟 mypython3.10 的環境

   ```sh
   conda activate mypython3.10
   ```

9. clone [openai/whisper 專案](https://github.com/openai/whisper) 到虛擬機內

   ```sh
   # 在這之前要先安裝 git 才可使用 git clone
   # yum install git -y
   pip install git+https://github.com/openai/whisper.git
   ```

10. 在youtube上找一部沒有字幕的影片並下載

   | 下載網址 | https://wave.video/convert/youtube-to-mp4-2 |
   | :------- | ------------------------------------------- |
   | 範例影片 | https://www.youtube.com/watch?v=CIVki2lLk30 |

11. 使用winscp連上將剛剛下載的影片複製過去

12. 連上 FTP 伺服器，將 test_whisper 複製到虛擬機內

    ```sh
    # yum install gftp -y
    gftp csie2.nqu.edu.tw
    ```

13. 進入到 test_whisper 資料夾內

    ```sh
    cd test_whisper
    ```

14. 將 gen.py 檔案複製到外面的目錄

    ```sh
    cp gen.py ../
    ```

15. 回到上一層資料夾

    ```sh
    cd ../
    ```

16. 編輯 gen.py 檔案

    ```sh
    gedit gen.py &
    ```

    ```sh
    # 將 Data_File 後面的檔案名稱改為要測試的檔案名稱
    Data_File = "123.mp4"
    ```

18. 安裝套件

    ```sh
    sudo yum localinstall --nogpgcheck https://download1.rpmfusion.org/free/el/rpmfusion-free-release-7.noarch.rpm
    ```

    ```sh
    sudo yum install ffmpeg ffmpeg-devel
    ```

19. 使用 python 開啟 gen.py

    ```sh
    python gen.py
    ```

19. 完成後會出現.str檔，用 winscp 將其複製到桌面，並使用 VLC media player 播放影片即可

![](https://github.com/Roy-Roo/Note/blob/main/111-2Linux%E7%B3%BB%E7%B5%B1%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/note/picture/week4/%E7%82%BA%E5%BD%B1%E7%89%87%E8%87%AA%E5%8B%95%E4%B8%8A%E5%AD%97%E5%B9%95-1.jpg)
