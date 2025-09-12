# 📒 DAY2 - 從零開始理財 Bot ！

---

## ⚙️ 前置作業

### 一、建立 LINE Bot UI

#### 1、 註冊 LINE Developers 帳號

1. 前往 [LINE Developers](https://developers.line.biz/)。

2. 使用 LINE 帳號登入。

<img src="https://github.com/user-attachments/assets/b2d0ccb6-8fe4-4aec-97dc-2ad3f6a9db00" alt="註冊LINE Developers帳號" width="900" />


---

#### 2、 建立 **Provider**

1. 在 Providers 點選 `Create` 建立 Provider（名稱可自訂，例如 `記帳Bot`）。

2. 在該 Provider 下建立 **Messaging API Channel**。

3. 在該 Provider 下建立 **Messaging API Channel** ，並填入必要資訊：

   * 帳號名稱：自訂（例如：「記帳Bot」）
   * Email：填入有效的電子郵件
   * 公司所在國家或地區：台灣
   * 業種：銀行、保險、金融 銀行、保險、金融（其他）

<img src="https://github.com/user-attachments/assets/48babfef-f08b-44a0-9786-626f9462d846" alt="建立 Provider" width="900" /> <img src="https://github.com/user-attachments/assets/c27c904e-7d3f-49b5-b892-68588dc3b72a" alt="Provider_linebot" width="900" /> <br><br>
<img src="https://github.com/user-attachments/assets/9b71e0a2-0b34-4f8c-a71b-f59d4b789697" alt="messaging api" width="900" /> <br><br>
<img src="https://github.com/user-attachments/assets/d9349bef-7257-424d-9003-d6ac70e7c7f2" alt="messaging" width="900" /> <br><br>
<img src="https://github.com/user-attachments/assets/16c98931-58bf-4657-b556-e6b552c6a4b8" alt="messaging info" width="900" />  <br><br>
<img src="https://github.com/user-attachments/assets/1e47f858-15c3-49a5-b562-1758e20cecb0" alt="messaging" width="450" /> 


---

#### 3、 記帳Bot 帳號設定

1. 進入 Channel 主頁，點選右上角「設定」。

2. 在「回應設定」中：

   * **僅開啟「加入好友的歡迎訊息」**

3. 點選下方 開啟「加入好友的歡迎訊息」設定畫面。

4. 在 「訊息設定」 中：
   編輯歡迎訊息，並儲存變更。範例如下：

   ```
   Hi, {Nickname}
   我是 {AccountName} 🤖
   開始使用請輸入：記帳  
   💰💰💰💰💰
   ```

5. 將「聊天的回應方式」設定為：

   * ✅ 手動聊天 + 自動回應訊息

<img src="https://github.com/user-attachments/assets/b6cfb7cc-37ae-43a7-9b7b-302ce32c8abd" alt="Bot設定" width="900" /> <br><br>
<img src="https://github.com/user-attachments/assets/f22b264d-d30a-4ac4-a28b-30446cf1f519" alt="回應設定中" width="900" /> <br><br>
<img src="https://github.com/user-attachments/assets/5c1d3309-8fb6-4078-9494-a12f8850ba9b" alt="歡迎訊息" width="900" /> <br><br>
<img src="https://github.com/user-attachments/assets/888224a6-0744-4813-9ada-cda87456931a" alt="回應方式" width="900" /> 


---

#### 4、 啟用 Messaging API

1. 在「設定」下， 點選下方「Messaging API」。

2. 點選「啟用Messaging API」。

3. 選擇剛建立的 Provider（如：記帳Bot），並按「同意」。

4. 記下 **Channel Secret**（之後會用到）。

<img src="https://github.com/user-attachments/assets/0fd4a846-43fc-4af3-85cf-61471fe14b9b" alt="啟用 Messaging API" width="900" /> <br><br>
<img src="https://github.com/user-attachments/assets/7b76c79b-3b4f-4783-b261-85fe34e09e01" alt="Channel Secret" width="900" /> 


---

#### 5、 取得 Channel API 資訊（‼️很重要）

1. 前往 LINE Developers Console，進入「記帳Bot」的 Channel 頁面。

2. 在 `Basic settings` 可以找到 **Channel Secret**。

3. 在 `Messaging API` 可以找到 **Channel Access Token** ，點選 **Issue** 取得。

4. 複製以下資訊：

   * → Channel Secret
   * → Channel Access Token

<img src="https://github.com/user-attachments/assets/4de017a5-bb99-4009-b2d8-2716e5d3d163" alt="Channel Access Token" width="500" /> <br><br>
 <img src="https://github.com/user-attachments/assets/2f0a364a-d8e2-45e0-8498-3c3de23cef05" alt="LINE Developers Channel 設定畫面" width="900" /> <img src="https://github.com/user-attachments/assets/c83269e3-a574-4746-956b-283f0270fe91" alt="LINE Developers Channel 設定畫面" width="900" />

---

### 二、Google 端設定

### 1、 雲端硬碟 & Google 試算表 設定

1. 在 Google 雲端硬碟建立資料夾，命名為 `linebot`。

2. 建立一份 Google 試算表，命名為 `linebot`。

3. 在試算表中建立三個工作表：

   #### ▸ 工作表一：控制變數

   * 在 `A1` 輸入 `0`（表示狀態）

   #### ▸ 工作表二：帳目資訊

   * 欄位：名稱 / 金額 / 時間 / 類別 / 總金額 / 記帳數目
   * 在「總金額」欄右方輸入公式：

     ```excel
     =SUM(B:B)
     ```
   * 在「記帳數目」欄右方輸入公式：

     ```excel
     =COUNTA(A3:A)
     ```

   #### ▸ 工作表三：帳目類別

   * 輸入自訂分類，範例分類如下：

     1 飲食 <br>
     2 交通 <br>
     3 購物 <br>
     4 ...

<img src="https://github.com/user-attachments/assets/4abbc790-d849-4a1f-9ffe-5837cea4494a" alt="工作表1" width="900" /> <br><br> 
<img src="https://github.com/user-attachments/assets/349de250-6f73-4383-96cd-d93f339160fa" alt="工作表2" width="900" /> <br><br>
<img src="https://github.com/user-attachments/assets/f04db476-ab08-48b8-84a1-c3856b96be1e" alt="工作表3" width="900" />


---

### 2、 Google Cloud 設定

1. 註冊 Google Cloud 帳號。

2. 前往：控制台 → IAM 與管理 → 服務帳戶 → 建立服務帳戶。

   * 服務帳戶名稱：`linebot`
   * 服務帳戶 ID：`linebot`
   * 點選：建立並繼續 → 完成

3. 建立金鑰：

   * 進入該服務帳戶的「金鑰」頁面。
   * 點選「新增鍵」 → 建立新的金鑰 → 選擇「JSON」。
   * 將 JSON 檔下載後命名為 `linebot.json`，並上傳至 雲端硬碟中 `linebot` 資料夾。

4. 服務帳戶 Email 共用試算表：

   * 在剛建立的 服務帳戶 中， 「詳細資訊」 中的 電子郵件 複製起來。
   * 前往剛建立的 Google 試算表。
   * 點選右上角「共用」。
   * 將該服務帳戶的 email 加入共用，權限設為「編輯」。

5. 啟用 API 權限：

   * 進入 控制台 → API 和服務 → API 程式庫
   * 在 Goole Workspace 下啟用 API：

     *  Google Drive API
     *  Google Sheets API


<img src="https://github.com/user-attachments/assets/3ea89607-1380-4cde-873c-dbc9411d0b23" alt="Google Cloud " width="900" /> <br><br> 
<img src="https://github.com/user-attachments/assets/46d821bb-fe08-4ada-a5e6-87b32536757b" alt="IAM 與管理" width="900" /> <br><br> 
<img src="https://github.com/user-attachments/assets/071e615c-2734-436a-94ea-7422a6da963f" alt="服務帳戶" width="900" /> <br><br> 
<img src="https://github.com/user-attachments/assets/f5f90eaf-d358-4902-b7ef-6eebb7f24ab8" alt="建立服務帳戶" width="900" /> <br><br> 
<img src="https://github.com/user-attachments/assets/1eb90760-26e2-41fb-aa86-115ee3e33b5d" alt="建立新的金鑰" width="900" /> 
<img src="https://github.com/user-attachments/assets/e24ef591-b58d-4487-8f60-5c13ccdd1da8" alt="linebot.json" width="450" /> <br><br> 
<img src="https://github.com/user-attachments/assets/179d0d37-b1b7-482b-9be3-6a87c2eb5558" alt="詳細資訊中的電子郵件" width="900" /> <br><br> 
<img src="https://github.com/user-attachments/assets/408ab0d7-ed3b-4497-b4d5-63358a500285" alt="共用權限" width="900" /> <br><br> 
<img src="https://github.com/user-attachments/assets/0355f204-6e80-4168-904f-9139723ea050" alt="API 程式庫" width="900" /> 


---
 <br>

## 💬 你已完成所有前置作業，接下來可以開始實作階段：正式啟動 LINE Bot！

 <br>
 
---

## 🤖 指令（在 LINE 傳訊給你的 Bot）

### 傳送以下格式的訊息：

| 指令           | 說明                                   |
| -------------- | ------------------------------------- |
| `記帳`         | 進入記帳系統                            |
| `1`            | 新增帳目（開始新增一筆記帳資料）         |
| `2`            | 新增類別（可新增分類）                  |
| `3`            | 進行結算                               |
| `4`            | 退出系統                               |
| `項目名稱 金額` | 輸入帳目，例如：`蛋餅 40`，須搭配`1新增帳目`使用    |
| 數字選項（1\~n）| 選擇所屬分類，例如：`1 飲食`、`2 交通`、`3 娛樂` 等 |
| `exit`         | 回到上一層選單                         |
| `重設代碼`      | 刪除所有紀錄資料                       |


 <br><br> 
<img src="https://github.com/user-attachments/assets/d6977ddd-9a86-4bfc-8ad0-0add1ea9b308" alt="指令" width="850" /> 


---
 <br><br> 
