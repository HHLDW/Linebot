# 建立 Line bot 基礎架構 + 記帳功能

## 今日目標

在前一天我們已經準備好Line Developers帳號、Channel、金鑰檔案 (linebot.json)

今天要做兩件事：

1. **建立基礎架構**：讓Line bot能夠接收訊息並回覆
2. **加入記帳功能**：輸入消費項目與金額，自動記錄到Google Sheet

---

## 安裝所需套件

```powershell
!nohup ssh -p 443 -o StrictHostKeyChecking=no -R0:localhost:5000 a.pinggy.io -T &
!cat nohup.out

!pip install flask
!pip install line-bot-sdk
!pip install pydrive
!pip install oauth2client
!pip install gspread
!pip install pygsheets
```

這些套件讓我們能連接Line Bot、建立伺服器、並存取 Google Sheet。

---

## 載入模組

```python
from flask import Flask, request
from linebot import LineBotApi, WebhookHandler
from linebot.models import *
from datetime import datetime, timezone, timedelta
from google.colab import drive
from oauth2client.service_account import ServiceAccountCredentials as SAC
from pydrive.auth import GoogleAuth
from pydrive.drive import GoogleDrive
import json, random, sys, pygsheets, gspread, os
```

匯入程式所需要的模組，包含Line Bot SDK、Flask、Google 驗證套件、以及 Python 內建的工具。

---

## 設定 Line Bot 與 Google Sheet

*Line Bot 驗證資訊*

```python
line_bot_api = LineBotApi 
handler = WebhookHandler
```

這裡要把 **你自己的金鑰** 換上去。

- ***LineBotApi** :*
    
    填入你的 Channel Access Token
    
- ***WebhookHandler** :*
    
    填入你的 Channel Secret (可以在 LINE Developers 的 Basic Settings 找到)
    

*掛載 Google Drive 與 Google Sheet 設定*

```python
drive.mount('/content/drive', force_remount=True)
GDriveJSON = 'linebot.json'
GSpreadSheet = 'linebot'
sheetKey = '你的 Google Sheet 網址'
sheet_url = f'<https://docs.google.com/spreadsheets/d/{GsheetKey}/>'
```

- ***GDriveJSON :***
    
    填入你的金鑰檔案名稱
    
- ***GSpreadSheet :***
    
    填入你的 Google Sheet 名稱
    
- ***sheetKey :***
    
    填入 Google Sheet 網址中 *d/ 到 /edit* 之間的字串
    
- ***sheet_url*** :
    
    在網址尾端 {GsheetKey}部分，同上填入Google Sheet 網址中 *d/ 到 /edit* 之間的字串
    

---

## 連線到 Google Sheet

```python
gauth = GoogleAuth()
scope_gd = ["<https://www.googleapis.com/auth/drive>"]
gauth.credentials = SAC.from_json_keyfile_name('/content/drive/MyDrive/linebot/linebot.json', scope_gd)
mygd = GoogleDrive(gauth)

try:
    scope_gs = ['<https://spreadsheets.google.com/feeds>']
    credentials = SAC.from_json_keyfile_name('/content/drive/MyDrive/linebot/linebot.json', scope_gs)
    gc = gspread.authorize(credentials)
    sheet = gc.open_by_url(sheet_url)

    Sheets_stage = sheet.get_worksheet(0)     
    Sheets_money = sheet.get_worksheet(1)     
    Sheets_category = sheet.get_worksheet(2)  

except Exception as ex:
    print('無法連線Google試算表', ex)
    sys.exit(1)
```

這段程式會打開 Google Sheet，並把三個工作表綁定

1. **stage**：存放 bot 狀態
2. **money**：記帳資料
3. **category**：消費分類

---

## 建立 Flask 伺服器

```python
app = Flask(__name__)

@app.route("/", methods=['POST'])
def linebot():
    body = request.get_data(as_text=True)
    json_data = json.loads(body)
    print(json_data)

    Time_now = datetime.now(timezone(timedelta(hours=+8),'CST'))
    event_num = int(Sheets_stage.cell(1,1).value)  # 從 Google Sheet 讀取目前狀態
```

Flask 讓我們在 Colab 上模擬一個小型伺服器，接收 Line Bot 的訊息。

---

## 記帳功能

以下是主要的互動流程：

### (1) 重設事件代碼

如果使用者輸入「重設」，會把狀態歸零。

```python
if '重設' in msg:
    text_message = TextSendMessage(text='已重設事件代碼')
    line_bot_api.reply_message(tk, text_message)
    event_num = 0
    Sheets_stage.update_cell(1,1,event_num)
```

### (2) 進入記帳系統

當 event_num = 0，輸入「記帳」即可進入主選單。

```python
while (event_num == 0):
    if '記帳' in msg:
        text_message = TextSendMessage(text='進入記帳系統')
        line_bot_api.reply_message(tk, [
            text_message,
            TextSendMessage(text="1 新增帳目\\n2 新增類別\\n3 進行結算\\n4 退出系統")
        ])
        event_num = 10
        Sheets_stage.update_cell(1,1,event_num)
        break
```

### (3) 新增帳目

輸入格式：「早餐 50」，Bot 會把項目名稱、金額、時間存進 Google Sheet。

```python
while (event_num == 11):
    if " " in msg:
        msg_s = msg.split(' ')
        item_name = msg_s[0]
        item_amount = int(msg_s[1])
        today = str(datetime.strftime(Time_now,'%Y-%m-%d %H:%M:%S'))
        item_data = [item_name, item_amount, today]
        Sheets_money.append_row(item_data,1)

        text_message = TextSendMessage(text='記帳資料已新增')
        text_message_1 = TextSendMessage(text="請輸入所屬類別")
```

### (4) 選擇分類

Bot 會顯示所有消費分類，讓使用者輸入編號。

- 例如輸入 `1` → 類別「餐飲」。

```python
while (event_num == 12):
    category_list = Sheets_category.get_all_values()
    msg_num = int(msg)

    if msg_num <= len(category_list):
        category = Sheets_category.cell(msg_num,2).value
        Sheets_money.update_cell(money_number,4,category)
        text_message = TextSendMessage(text="已寫入類別：" + category)
```

### (5) 新增類別

使用者可以新增新的消費分類，例如「交通」、「娛樂」。

```python
while (event_num == 13):
    category_name = msg
    category_data = [category_number+1, category_name]
    Sheets_category.append_row(category_data,1)
    text_message = TextSendMessage(text=f"類別 {category_name} 已新增")
```

### (6) 查看總結

使用者輸入 `3`，Bot 會顯示目前總花費與所有紀錄。

```python
if '3' in msg:
    total_amount = str(Sheets_money.cell(1,6).value)
    text_message = TextSendMessage(text=f"目前總共花費 {total_amount} 元")
    money_list = Sheets_money.get_all_values()
    money_write = ""
    for row in money_list:
        money_write += '\\t'.join(row) + '\\n'
    line_bot_api.reply_message(tk, [
        TextSendMessage(text=money_write),
        text_message
    ])
```

## 7. 啟動伺服器

最後讓 Flask 開始運行：

```python
if __name__ == "__main__":
    app.run()
```

---

## ✅ 今日小結

今天我們完成了以下三件事：

- 建立 **Line Bot 基礎架構**
- 串接 **Google Sheet**
- 實作了 **記帳系統（新增帳目、分類、查看總花費）**

明天我們會繼續擴充功能，例如 **匯率換算**，讓 Line Bot 更貼近「理財助理」！
