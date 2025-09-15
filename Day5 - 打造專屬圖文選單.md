# **Line Bot 鐵人賽第五天：打造你的專屬圖文選單**

歡迎來到鐵人賽的第五天！

前幾天我們完成了記帳和匯率功能，但你可能發現，每次都要手動輸入特定的關鍵字才能使用，這對使用者來說有點不方便。

今天，我們要讓 Bot 的介面變得更直覺、更專業！我們要利用 **LINE Bot 的圖文選單**，將這些功能變成可點擊的按鈕，讓用戶只要按一下圖示，就能快速執行你想要的功能。

準備好了嗎？讓我們一起進入 LINE Bot 後台，建立你的第一個圖文選單吧！

---

### **步驟一：建立圖文選單**

1.  **進入後台**：
    首先，登入你的 LINE Official Account Manager，在主頁裡的左側選單中找到「**圖文選單**」選項，然後點擊「**建立**」來新增一個圖文選單。
    
<img width="865" height="401" alt="image" src="https://github.com/user-attachments/assets/e815ca94-008c-4a48-9006-5e20ea30e54a" />

2.  **基本設定**：
    * **標題**：為你的圖文選單命名。這個名稱只會顯示在後台，方便你管理，例如：「主選單」或「功能選單」。
    * **使用時間**：你可以設定這個圖文選單的有效期間，例如：從今天開始，持續到專案結束。

<img width="865" height="372" alt="image" src="https://github.com/user-attachments/assets/728311bb-7fca-46bc-b8d4-ecc2f52aab5e" />

---

### **步驟二：內容設定**

這是設計圖文選單外觀的核心步驟。

<img width="865" height="445" alt="image" src="https://github.com/user-attachments/assets/45c148a7-f179-4c92-92b5-64885360334c" />

1.  **版型選擇**：
    在「**內容設定**」中，你可以選擇圖文選單的版型。LINE 提供了多種選項，常見的有「**大型**」和「**小型**」。你可以根據 Bot 的功能多寡和介面需求來選擇合適的版型並套用。

<img width="865" height="857" alt="image" src="https://github.com/user-attachments/assets/d9ca6797-1723-4e4b-b5f8-07c9ffa654ea" />

2.  **圖片設計**：
    選擇並套用版型後，你可以開始設計圖片。你有兩種方式：
    * **單一背景**：如果你想讓整個圖文選單的背景保持一致，可以上傳一張完整的背景圖片。
    * **區塊式圖片**：你也可以為每個區塊（也就是每個按鈕）單獨設定圖片，讓每個按鈕都有不同的視覺效果。

<img width="865" height="521" alt="image" src="https://github.com/user-attachments/assets/daa786fa-aef0-465d-a9c2-da41b9fd449a" />

<img width="865" height="556" alt="image" src="https://github.com/user-attachments/assets/1944189d-a44d-425f-adba-b03467ccd210" />

3.  **使用小工具**：
    LINE 後台提供了內建的小工具，可以幫助你快速設計：
    * **文字圖片背景**：可以選擇單色背景，並加上文字或簡單的圖案。
    * **框線**：可以調整按鈕之間的框線，讓整體設計更有個人風格。
    * **圖示**：你可以選擇與功能相符的圖示，例如用錢包圖示代表「記帳」，讓用戶一目了然。

<img width="865" height="551" alt="image" src="https://github.com/user-attachments/assets/0a2f28ef-e3e6-4bfa-bcf0-cb773f156c83" />

---

### **步驟三：設定按鈕動作**

設計好外觀後，我們要來設定每個按鈕的功能。

1.  **動作類型**：
    在「**動作**」頁面，點擊每個區塊，你可以選擇以下動作類型：
    * **連結 (Link)**：點擊後會開啟外部網頁。
    * **優惠券 (Coupon)**：點擊後會顯示你設定好的優惠券。
    * **文字 (Text)**：點擊後會自動傳送一段文字到聊天室。
    * **集點卡 (Points)**：點擊後會顯示集點卡資訊。
    * **不設定 (None)**：如果你只是為了美觀，不希望這個區塊有任何功能，可以選擇「不設定」。

<img width="865" height="556" alt="image" src="https://github.com/user-attachments/assets/14b27617-ee9d-4cc3-bbe7-941bb044ad64" />

2.  **設定提示語與連結**：
    * **快樂夥伴區塊**：我將這個區塊的動作設為「**連結**」，並貼上我們團隊的介紹頁面網址。
    * **記帳、匯率、重設代碼區塊**：這些功能需要 Bot 收到特定的關鍵字才會執行，所以我們將動作都設定為「**文字**」，並在下方的方框中分別輸入對應的提示語，例如：「記帳」、「匯率」、「重設代碼」。

<img width="865" height="524" alt="image" src="https://github.com/user-attachments/assets/11624c0e-0bbe-46a8-bce0-5f1f055b1d88" />

3.  **設定選單列顯示文字**：
    在最下方，你可以自訂選單列的顯示文字。這個文字會和圖文選單一起顯示在你的 LINE 頁面上，讓用戶知道可以點擊這個選單。

<img width="865" height="245" alt="image" src="https://github.com/user-attachments/assets/d2a8c180-9bd5-4be9-8511-9cfc705e5b9e" />

---

### **步驟四：儲存與完成**

完成所有設定後，點擊頁面右下角的「**儲存**」按鈕。現在，你的圖文選單就建立完成了！你可以在後台的圖文選單列表頁面看到你剛才設計的成果。

下一個步驟，就是將這個圖文選單設為預設，讓所有用戶都能看到它！

<img width="865" height="404" alt="image" src="https://github.com/user-attachments/assets/79c29fbe-72bb-4bfe-954f-37d015102ba5" />

---

### 功能展示

<div style="display: flex; justify-content: center; gap: 20px;">
  <img src="https://github.com/user-attachments/assets/238e6b0d-5573-43bc-ad19-6e8c6de13425" alt="YPSY SY 網站截圖" style="width: 48%; height: auto; object-fit: contain; border: 1px solid #ddd; border-radius: 8px; box-shadow: 2px 2px 5px rgba(0,0,0,0.2);">
  <img src="https://github.com/user-attachments/assets/7d8bb89b-0e63-48ff-b5f4-2a5bc4f510f2" alt="LINE Bot 圖文選單截圖" style="width: 48%; height: auto; object-fit: contain; border: 1px solid #ddd; border-radius: 8px; box-shadow: 2px 2px 5px rgba(0,0,0,0.2);">
</div>



