# **Line Bot 鐵人賽第五天：打造專屬圖文選單**

歡迎來到鐵人賽的第五天！

前幾天我們完成了記帳和匯率查詢功能，但有個小缺點：每次都要手動輸入特定的關鍵字，功能才會跑出來。

今天，我們要讓 Bot 的介面變得更直覺、更美觀！我們要利用**圖文選單**，把那些關鍵字變成一個個按鈕，以後只要輕鬆點一下，就能啟動你想要的功能。

準備好了嗎？讓我們開始吧！

---

## **步驟一：建立圖文選單**

1.  **進入後台**：
    首先，登入你的 LINE Developers 帳號，找到你的 Bot，然後進入**主頁**。在左側選單中，點擊 訊息項目裡的**圖文訊息**來建立你的選單。
    
<img width="800" height="400" alt="image" src="https://github.com/user-attachments/assets/71d5a13c-8507-4a41-8b9b-b1a3c4c24789" />

2.  **建立項目**：
    點擊**建立**，然後為你的圖文選單命名。你可以根據喜好來命名，例如：「主選單」或「功能選單」。

<img width="800" height="150" alt="image" src="https://github.com/user-attachments/assets/ce04cbd9-40b2-445b-ac20-d28df015bd5b" />

---

## **步驟二：設計圖文選單的樣式**

LINE 在今年對圖文選單的設計介面進行了改版，變得更方便了。

1.  **選擇背景圖片**：
    在訊息設定裡，你會看到**設計規範**的選項。點擊進去，你可以上傳自己設計的圖片作為圖文選單的背景。
    * **圖片尺寸**：圖片尺寸必須嚴格遵守規範，例如 `1040px x 350px`。

<img width="800" height="554" alt="image" src="https://github.com/user-attachments/assets/89fd1345-0df1-4502-bad1-0093e362e8df" />


2.  **選擇版型**：
    這次，我們選擇最常見的「**四宮格**」版型。當然，你也可以根據自己的需求選擇其他版型。如果你想用自己的背景圖，點擊**上傳背景圖片**即可。

<img width="800" height="297" alt="image" src="https://github.com/user-attachments/assets/55738422-262f-4fd4-b462-44bde8852d9a" />

<img width="800" height="416" alt="image" src="https://github.com/user-attachments/assets/ea60b43c-dca8-4f4d-a0ea-a17f130052a7" />


3.  **自訂風格**：
    即使沒有自己設計的圖片，你也可以點選**文字圖片背景**或**框線樣式**來選擇單色背景或調整框線，打造出獨特的個人風格。
    
<img width="800" height="567" alt="image" src="https://github.com/user-attachments/assets/144ad518-7c37-4aa8-8797-403eebf61a8a" />

---

## **步驟三：設定按鈕動作**

設計好選單外觀後，我們要來設定每個區塊（也就是按鈕）的功能。

1.  **進入動作設定**：
    切換到**動作**頁面，你會看到四個預設的區塊（A、B、C、D）。

2.  **選擇類型**：
    點擊每個區塊，在**類型**選項中，你可以選擇：
    * **連結**：點擊後會開啟網頁。
    * **優惠券**：點擊後會顯示優惠券。
    * **文字**：點擊後會自動發送一段文字。
      
<img width="800" height="400" alt="image" src="https://github.com/user-attachments/assets/9bc17f81-5078-422b-ad01-f84f0dbaf9fe" />

3.  **設定提示語**：
    * 我們將「**記帳**」、「**匯率**」和「**重設代碼**」這三個按鈕的類型都設定為**文字**。
    * 在下方的文字方框中，分別輸入對應的提示語，例如：「記帳」、「匯率」、「重設代碼」。
    * 這樣一來，當用戶點擊這些按鈕時，Bot 就會像收到文字一樣，啟動對應的功能。
      
<img width="865" height="713" alt="image" src="https://github.com/user-attachments/assets/7741d155-87fc-4351-ab30-0f028cb293db" />

4.  **設定外部連結**：
    * 最後，你可以將最後一個按鈕設定為**連結**。
    * 選擇「連結」類型，並在下方的方框中貼上你想要的網址，例如你的個人網站或 GitHub 專案頁面。

---

## **步驟四：儲存與發布**

1.  **儲存設定**：
    完成所有設定後，點擊頁面右下角的**儲存**。

2.  **發布選單**：
    儲存後，你的圖文選單就建立完成了。這樣所有的用戶都能看到你設計的選單。
    
<img width="800" height="416" alt="image" src="https://github.com/user-attachments/assets/b3b89d14-b07b-4011-891b-c6bf683f7b0b" />

現在打開你的 LINE Bot 頁面，就可以看到你親手設計的精美圖文選單了！它讓你的 Bot 變得更專業，也讓用戶能更輕鬆地使用你的功能。
