# 📒 DAY4 - Line Bot 記帳教學：整合匯率查詢功能

今天的教學將引導你如何在 Google Colab 環境中，為你的 Line 記帳 Bot 增加**匯率查詢**功能。

---

## **步驟一：安裝新工具**

你的舊程式碼只會處理記帳，我們需要讓程式能連上網路去查詢匯率，這就像是幫你的 Google Colab 筆記本安裝一個新的 App。

**首先與記帳程式碼一樣**
```
!nohup ssh -p 443 -o StrictHostKeyChecking=no -R0:localhost:5000 a.pinggy.io -T &
```
```
!cat nohup.out
```

請在 Colab 的程式碼區塊中，輸入以下指令並執行：

```bash
!pip install flask
!pip install line-bot-sdk
!pip install pydrive
!pip install oauth2client
!pip install gspread
!pip install pygsheets
!pip install twder
```
*!pip install twder：這行指令會安裝一個名為 twder 的套件。這個套件就是我們需要的「匯率查詢 App」，它會自動幫你的程式處理複雜的資料抓取。*

---

## **步驟二：程式碼的"狀態機"**

新程式碼使用了 **「狀態機」** 這個概念，來判斷你目前在 Bot 的哪一個功能裡。

你可以把你的 Bot 想像成一個有不同房間的房子，而 event_num 就是一個會顯示在門口的「門牌號碼」。

  + event_num == 0 (大廳)：Bot 剛啟動時就在這裡。當你在 Line 裡輸入「匯率」時，Bot 會把門牌號碼換成 20，帶你進入「匯率房間」。

  + event_num == 10 (記帳房間)：當你進入這個房間後，Bot 就只會處理和記帳相關的指令。

  + event_num == 20 (匯率房間)：在這個房間裡，Bot 只會處理和匯率查詢相關的指令。

---

+ ### 「重設代碼」功能

*你的程式碼中還有一個特別的指令：「重設代碼」。*

這個指令就像是一個「緊急門」，無論你現在在哪個房間（event_num 是多少），只要你輸入「重設代碼」，Bot 就會強制將門牌號碼改回 0，讓你回到「大廳」的初始狀態。這個功能非常實用，當你不小心進入錯誤的選單或卡住時，就可以用它來重新開始。

---

## **步驟三：匯率功能的程式碼解密**
新的程式碼中，所有關於匯率的功能都集中在 while (event_num == 20): 這個區塊裡。這段程式碼主要有三個功能，都是透過 twder 這個套件來實現的。

### 查詢所有匯率

  + 發生時機：當你在匯率選單（event_num == 20）輸入「1」時。

  + 程式碼運作：程式會執行 twder.now_all() 這個指令。

  + 指令說明：twder.now_all() 這個函式會瞬間連上台灣銀行的網站，抓取所有貨幣的即時匯率。程式會將這些雜亂的資料整理成一串易於閱讀的清單，然後回傳給你。

### 查詢昨日匯率

  + 發生時機：當你輸入「2」時，Bot 會請你輸入貨幣代碼（例如：USD、JPY）。

  + 程式碼運作：你輸入代碼後，程式會執行 twder.past_day(msg)。

  + 指令說明：twder.past_day(msg) 這個函式會根據你輸入的代碼（msg），去查詢這個貨幣前一天的匯率。

### 查詢幣別代碼

  + 發生時機：如果你不知道要輸入什麼代碼，輸入「3」時。

  + 程式碼運作：程式會執行 twder.currency_name_dict()。

  + 指令說明：twder.currency_name_dict() 這個函式會回傳所有貨幣的名稱和代碼對照表，讓你方便找到正確的代碼。

---
**加入匯率的程式碼**
```
from flask import Flask, request
from linebot import LineBotApi, WebhookHandler
from linebot.models import *
from datetime import datetime, timezone, timedelta
from google.colab import drive
from oauth2client.service_account import ServiceAccountCredentials as SAC
from pydrive.auth import GoogleAuth
from pydrive.drive import GoogleDrive

import json
import random
import sys
import pygsheets
import gspread
import os
import requests #負責傳送資料或觸發API
import twder #匯入twder套件，它是專門用來擷取台灣銀行的新台幣匯率資料的工具。


line_bot_api = LineBotApi('Channel access token') #填寫你的Channel access token
handler = WebhookHandler('Channel secret') #填寫你的Channel secret

drive.mount('/content/drive', force_remount=True)
GDriveJSON = 'linebot.json'
GSpreadSheet = 'linebot'
GsheetKey = 'google sheet 網址' #填寫你的google sheet網址(/d/後面直到/exit)
sheet_url='https://docs.google.com/spreadsheets/d/GsheetKey' #GsheetKey與上面相同

gauth = GoogleAuth()
scope_gd = ["https://www.googleapis.com/auth/drive"]
gauth.credentials = SAC.from_json_keyfile_name('/content/drive/MyDrive/linebot/linebot.json', scope_gd) #你的linebot.json 位於雲端硬碟的路徑
mygd = GoogleDrive(gauth)

try:
  scope_gs = ['https://spreadsheets.google.com/feeds']
  credentials = SAC.from_json_keyfile_name('/content/drive/MyDrive/linebot/linebot.json', scope_gs) #你的linebot.json 位於雲端硬碟的路徑
  gc = gspread.authorize(credentials)
  sheet = gc.open_by_url(sheet_url)
  Sheets_stage=sheet.get_worksheet(0)
  Sheets_money=sheet.get_worksheet(1)
  Sheets_category=sheet.get_worksheet(2)


except Exception as ex:
  print('無法連線Google試算表', ex)
  sys.exit(1)

event_num=int(Sheets_stage.cell(1,1).value)

os.chdir('/content/drive/MyDrive/linebot')  # Colab 換路徑使用

app = Flask(__name__)
app = Flask(__name__)

# Flask 和 Line Bot 相關設置
line_bot_api = LineBotApi('Channel access token') #填寫你的Channel access token
handler = WebhookHandler('Channel secret') #填寫你的Channel secret

app = Flask(__name__)

@app.route("/", methods=['POST'])
def linebot():
    body = request.get_data(as_text=True)
    json_data = json.loads(body)
    print(json_data)
    Time_now = datetime.now(timezone(timedelta(hours=+8),'CST'))
    event_num = int(Sheets_stage.cell(1,1).value)
    print(Time_now)
    try:
        signature = request.headers['X-Line-Signature']
        handler.handle(body, signature)
        tk = json_data['events'][0]['replyToken']      # 取得 reply token
        msg_type = json_data['events'][0]['message']['type'] # 取得 LINE 收到的訊息類型
        if msg_type == 'text':
          msg = json_data['events'][0]['message']['text']   # 取得使用者發送的訊息
        msgID = json_data['events'][0]['message']['id']
        print(msgID)
        print(msg_type)
        ai_msg = msg[:2].lower()
        reply_msg = ''
        if '重設代碼' in msg:
          text_message = TextSendMessage(text='事件代碼已重設')
          event_num = 0
          Sheets_stage.update_cell(1,1,event_num)
          line_bot_api.reply_message(tk,text_message)
        while (event_num == 0):
          if '記帳' in msg:
            text_message = TextSendMessage(text='進入記帳系統')
            line_bot_api.reply_message(tk,[text_message, TextSendMessage(text="1 新增帳目"+"\n"+"2 新增類別"+"\n"+"3 進行結算"+"\n"+"4 退出系統")])
            event_num = 10
            Sheets_stage.update_cell(1,1,event_num)
            break
          if '匯率' in msg:
            text_message = TextSendMessage(text='匯率查詢系統')
            text_message = TextSendMessage(text='輸入幣別代碼可查詢現在匯率')
            line_bot_api.reply_message(tk,[text_message, TextSendMessage(text="1 所有幣別匯率"+"\n"+"2 昨日匯率查詢"+"\n"+"3 幣別代碼查詢"+"\n"+"4 退出系統")])
            event_num = 20
            Sheets_stage.update_cell(1,1,event_num)
            break
            return 'OK'
        while (event_num == 11):
          if " " in msg:
            msg_s = msg.split(' ')
            item_name = msg_s[0]
            item_amount = int(msg_s[1])
            today = str(datetime.strftime(Time_now,'%Y-%m-%d %H:%M:%S'))
            item_data=[item_name,item_amount,today]
            Sheets_money.append_row(item_data,1)
            text_message = TextSendMessage(text='記帳資料已新增')
            text_message_1 = TextSendMessage(text="請輸入所屬類別")
            category_list = Sheets_category.get_all_values()
            print(category_list)
            category_number = len(category_list)
            print(category_number)
            category_list_show = ""
            for list_num in range(0,category_number,1):
              category_num, category_name = category_list [list_num]
              category_list_name = category_num+"\t"+category_name+"\n"
              category_list_show = category_list_show + category_list_name
            print(category_list_show)
            text_message_2 = TextSendMessage(text=category_list_show)
            line_bot_api.reply_message(tk,[TextSendMessage(text=msg+"\n"),text_message,text_message_1,text_message_2])
            event_num = 12
            Sheets_stage.update_cell(1,1,event_num)
            break
          if 'exit' in msg:
            text_message = TextSendMessage(text='返回記帳系統選單')
            line_bot_api.reply_message(tk,[text_message, TextSendMessage(text="1 新增帳目"+"\n"+"2 新增類別"+"\n"+"3 進行結算"+"\n"+"4 退出系統")])
            event_num = 10
            Sheets_stage.update_cell(1,1,event_num)
            break
        while (event_num == 12):
          category_list = Sheets_category.get_all_values()
          category_number = len(category_list)
          money_list = Sheets_money.get_all_values()
          money_number = len(money_list)
          msg_num = int(msg)
          if msg_num > category_number:
            text_message = TextSendMessage(text='請輸入正確類別編號')
            line_bot_api.reply_message(tk,text_message)
            event_num = 12
            Sheets_stage.update_cell(1,1,event_num)
            break
          if msg_num <= category_number:
            category = Sheets_category.cell(msg_num,2).value
            Sheets_money.update_cell(money_number,4,category)
            text_message = TextSendMessage(text="已寫入類別"+"\t"+category)
            line_bot_api.reply_message(tk,[text_message, TextSendMessage(text="1 新增帳目"+"\n"+"2 新增類別"+"\n"+"3 進行結算"+"\n"+"4 退出系統")])
            event_num = 10
            Sheets_stage.update_cell(1,1,event_num)
            break
        while (event_num == 13):
          if 'exit' in msg:
            text_message = TextSendMessage(text='返回記帳系統選單')
            line_bot_api.reply_message(tk,[text_message, TextSendMessage(text="1 新增帳目"+"\n"+"2 新增類別"+"\n"+"3 進行結算"+"\n"+"4 退出系統")])
            event_num = 10
            Sheets_stage.update_cell(1,1,event_num)
            break
          category_name = msg
          category_list = Sheets_category.get_all_values()
          category_number = len(category_list)
          category_data = [category_number+1, category_name]
          Sheets_category.append_row(category_data,1)
          text_message = TextSendMessage(text="類別"+"\t"+category_name+"\t"+"已新增")
          line_bot_api.reply_message(tk,[text_message, TextSendMessage(text="1 新增帳目"+"\n"+"2 新增類別"+"\n"+"3 進行結算"+"\n"+"4 退出系統")])
          event_num = 10
          Sheets_stage.update_cell(1,1,event_num)
          break
        while (event_num == 10):
          if '1' in msg:
            text_message = TextSendMessage(text='請輸入項目名稱與金額，並使用空白區隔，例如：早餐 50。或輸入exit回到上一層')
            line_bot_api.reply_message(tk,text_message)
            event_num = 11
            Sheets_stage.update_cell(1,1,event_num)
            break
          if '2' in msg:
            text_message = TextSendMessage(text='請輸入類別名稱。或輸入exit回到上一層')
            line_bot_api.reply_message(tk,text_message)
            event_num = 13
            Sheets_stage.update_cell(1,1,event_num)
            break
          if '3' in msg:
            total_amount = str(Sheets_money.cell(1,6).value)
            print(total_amount)
            text_message = TextSendMessage(text="目前總共花費"+"\t"+total_amount+"\t"+"元")
            money_list = Sheets_money.get_all_values()
            money_number = len(money_list)
            print(money_number)
            money_write=""
            for listnumber in range(money_number):
              money_name = money_list[listnumber][0]
              money_amount = money_list[listnumber][1]
              money_date = money_list[listnumber][2]
              money_category = money_list[listnumber][3]
              money_list_write = money_name+'\t'+money_amount+'\t'+money_date+'\t'+money_category+'\n'
              money_write = money_write + money_list_write
            line_bot_api.reply_message(tk,[TextSendMessage(text=money_write),text_message,TextSendMessage(text="1 新增帳目"+"\n"+"2 新增類別"+"\n"+"3 進行結算"+"\n"+"4 退出系統")])
            break
          if '4' in msg:
            text_message = TextSendMessage(text='已退出記帳系統')
            line_bot_api.reply_message(tk,text_message)
            event_num = 0
            Sheets_stage.update_cell(1,1,event_num)
            break
          text_message = TextSendMessage(text='輸入錯誤，請重新輸入')
          line_bot_api.reply_message(tk,text_message)
          break

        while (event_num == 21):
          if 'exit' in msg:
            text_message = TextSendMessage(text='返回匯率系統選單')
            line_bot_api.reply_message(tk,[text_message, TextSendMessage(text="1 所有幣別匯率"+"\n"+"2 昨日匯率查詢"+"\n"+"3 幣別代碼查詢"+"\n"+"4 退出系統")])
            event_num = 20
            Sheets_stage.update_cell(1,1,event_num)
            break
          currency_past=twder.past_day(msg)
          number_currency_past=len(currency_past)
          number_currency_past_column=len(currency_past[0])
          currency_past_list=list(currency_past[number_currency_past-1])
          currency_past_data_write=''
          currency_list_past = '時間'+'\t'+'現金買入'+'\t'+'現金賣出'+'\t'+'即期買入'+'\t'+'即期賣出'
          for currency_list_past_num in range(number_currency_past_column):
            currency_past_data=currency_past_list[currency_list_past_num]
            currency_past_data_write = currency_past_data_write + currency_past_data + '\t'
          currency_list_past = currency_list_past + '\n' + currency_past_data_write
          event_num = 20
          Sheets_stage.update_cell(1,1,event_num)
          line_bot_api.reply_message(tk,[TextSendMessage(text=currency_list_past),TextSendMessage(text="1 所有幣別匯率"+"\n"+"2 昨日匯率查詢"+"\n"+"3 幣別代碼查詢"+"\n"+"4 退出系統")])

        while (event_num == 20):
          try:
            if '1' in msg:
              currency_all_code = list(twder.now_all().keys())
              currency_all_value = list(twder.now_all().values())
              currency_all_code_len = len(currency_all_code)
              currency_all_value_len = len(currency_all_value[0])
              currency_all_write = ""
              currency_all_value_list_write = ""
              currency_all_code_list_write = ""
              currency_all_list = '幣別' + '\t' + '時間' + '\t' + '現金買入' + '\t' + '現金賣出' + '\t' + '即期買入' + '\t' + '即期賣出'
              for currency_all_code_num in range(currency_all_code_len):
                currency_all_code_list = str(currency_all_code[currency_all_code_num])
                for currency_all_value_num in range(currency_all_value_len):
                  currency_all_value_list = str(currency_all_value[currency_all_code_num][currency_all_value_num])
                  currency_all_value_list_write = currency_all_value_list_write + '\t' + currency_all_value_list
                currency_all_code_list_write = currency_all_code_list_write + currency_all_code_list + '\t' + currency_all_value_list_write + '\n'
                currency_all_value_list_write = ''
              currency_all_write = currency_all_list + ' \n' + currency_all_code_list_write
              text_message = TextSendMessage(text=currency_all_write)
              line_bot_api.reply_message(tk,[text_message,TextSendMessage(text="1 所有幣別匯率"+"\n"+"2 昨日匯率查詢"+"\n"+"3 幣別代碼查詢"+"\n"+"4 退出系統")])
              break

            if '2' in msg:
              text_message = TextSendMessage(text='請輸入貨幣英文代碼。或輸入exit回到上一層')
              line_bot_api.reply_message(tk,text_message)
              event_num = 21
              Sheets_stage.update_cell(1,1,event_num)
              break

            if '3' in msg:
              currency_name_code_dist = twder.currency_name_dict()
              number_currency = len(currency_name_code_dist)
              currency_name_code_list=list(currency_name_code_dist.values())
              currency_list = ""
              for currency_num in range(number_currency):
                currency_code = currency_name_code_list[currency_num]
                currency_code_write = currency_code+'\n'
                currency_list = currency_list + currency_code_write
              line_bot_api.reply_message(tk,[TextSendMessage(text=currency_list),TextSendMessage(text="1 所有幣別匯率"+"\n"+"2 昨日匯率查詢"+"\n"+"3 幣別代碼查詢"+"\n"+"4 退出系統")])
              break

            if '4' in msg:
              text_message = TextSendMessage(text='已退出匯率系統')
              line_bot_api.reply_message(tk,text_message)
              event_num = 0
              Sheets_stage.update_cell(1,1,event_num)
              break

            currency_now=twder.now(msg)
            number_currency_now=len(currency_now)
            currency_now_list=list(currency_now)
            currency_now_data_write=''
            currency_list_now = '時間'+'\t'+'現金買入'+'\t'+'現金賣出'+'\t'+'即期買入'+'\t'+'即期賣出'
            for currency_list_now_num in range(number_currency_now):
              currency_now_data=currency_now_list[currency_list_now_num]
              currency_now_data_write = currency_now_data_write + currency_now_data + '\t'
            currency_list_now = currency_list_now + '\n' + currency_now_data_write
            line_bot_api.reply_message(tk,[TextSendMessage(text=currency_list_now),TextSendMessage(text="1 所有幣別匯率"+"\n"+"2 昨日匯率查詢"+"\n"+"3 幣別代碼查詢"+"\n"+"4 退出系統")])
          except:
            text_message = TextSendMessage(text='輸入錯誤，請重新輸入')
            line_bot_api.reply_message(tk,[text_message,TextSendMessage(text="1 所有幣別匯率"+"\n"+"2 昨日匯率查詢"+"\n"+"3 幣別代碼查詢"+"\n"+"4 退出系統")])
            break
          return 'OK'


    except:
        print('error')
    return 'OK'

if __name__ == "__main__":
    app.run()
```
## **步驟四：運行程式並測試新功能**

 *後面的步驟與前面記帳linebot步驟一樣*

1.啟動 Bot之後，將你提供的所有程式碼貼到 Colab 的一個新程式碼區塊中，然後執行它。

2.程式會開始運行，並在輸出中顯示一個公開的網址（通常是 ngrok 提供的）。

3.設定 Webhook，複製這個公開網址。

4.登入你的 LINE Developers 後台，找到 Webhook 網址欄位並將複製的網址貼上，在網址後方加上 /。例如：https://xxxx.ngrok-free.app/。

5.點擊「驗證」，確保連線成功。

現在，你就可以在你的 Line Bot 聊天室中，輸入「匯率」來測試你的新功能了！

### 傳送以下格式的訊息：

| 新指令           | 說明                                   |
| -------------- | ------------------------------------- |
| `匯率`         | 輸入幣別代碼可查詢現在匯率               |
| `1`            | 所有幣別匯率                           |
| `2`            | 昨日匯率查詢(輸入幣別代碼來查詢）        |
| `3`            | 幣別代碼查詢                           |
| `4`            | 退出系統                               |
| `exit`         | 回到上一層選單                         |
| `重設代碼`      | 刪除所有紀錄資料                       |

---
# 成功介面

<div style="display: flex; justify-content: space-around;">
  <img src="https://github.com/user-attachments/assets/25d1f148-34d9-4991-9cd1-6aa27dba31b2" width="47.5%">
  <img src="https://github.com/user-attachments/assets/d26bcf06-979a-4438-8154-5ccac25a3bd1" width="45%">
</div>

















