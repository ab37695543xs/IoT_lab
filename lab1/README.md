# 物聯網 Lab1
物聯網目錄：
* [物聯網環境建置](/9cAzkUsBRZCozYU6PdEXDA)
* 本篇 (Lab1)
* [物聯網 Lab2](/I4zHw3uDTneV2Jw3G829kQ)
* [物聯網 Lab4](/Tm65KQpQR0aUrsrUTlGgzQ)
* [物聯網 Lab5](/pZzuShDZQ1qnUSOQ9RTgYg)

## 猜數字
### 達成目標
基本功能：
- [x] 亂數產生 1~100
- [x] 比對結果並顯示
- [x] 計數
- [x] 關於程式
- [x] 遊戲重啟

額外功能：
- [x] 猜錯時給予提示，告知更大或更小
- [x] 輸入防呆，避免 `TextBox` 為空或不在 1~100 內
- [x] 重啟遊戲時清除輸入

> 題外話：即使將內容複製到新的 project，APK 安裝後的名稱還是會和舊的一樣，這部份似乎無解

### 版面設計
<p float="left">
  <img src="https://i.imgur.com/chCYAQY.png" width="50%" />
  <img src="https://i.imgur.com/wbTHacH.png" width="40%" /> 
</p>

### 程式設計
```python=
int answer = rand(1, 100)
int count = 0

if btn_guess.Click:
    if (TextBox1.Text not null) and (TextBox1.Text >= 1 and TextBox1.Text <= 100):
        count = count + 1
        lab_counter.Text = "You've tried " + count + " turns!"
        if (TextBox1.Text == answer):
            lab_info.Text = "Congratulations! You got the answer!"
            btn_guess.Enabled = false
        else if (TextBox1.Text < answer):
            lab_info.Text = "Try bigger number!"
        else if (TextBox1.Text > answer):
            lab_info.Text = "Try smaller number!"

if btn_restart.Click:
    answer = rand(1, 100)
    count = 1
    lab_info.Text = "Enter a number between 1 and 100"
    lab_counter.Text = "You've tried 0 turns!"
    TextBox1.Text = ""
    btn_guess.Enabled = true

if btn_abount.Click:
    Notifier1.ShowMessageDialog(message="Author: Bill Sun",
                                title="About",
                                buttonText="Ok")

if btn_quit.Click:
    close_application()
```
核心部分：
![](https://i.imgur.com/MoVZPTK.png)

### 實際畫面
![](https://i.imgur.com/xv7gM7x.png)


## HTTP POST 文字
### 達成目標
基本功能：
- [x] 將輸入文字送出 POST 請求
- [x] Server 處理請求並回傳歡迎資訊

額外功能：
- [x] 輸入防呆，避免 `TextBox` 為空
- [x] 將歡迎訊息改由 `Notifier` 顯示，可節省重置的按鈕，以確保每次發送都是新的請求

### 版面設計
<p float="left">
  <img src="https://i.imgur.com/x5EncwE.png" width="50%" />
  <img src="https://i.imgur.com/Zw1vObl.png" width="40%" /> 
</p>

### 程式設計
由於安卓模擬器無法直接連到 `localhost`，需直接透過 IPv4 位址存取，在 cmd 輸入 `ipconfig` 以查看 IP
> 根據官方的[設置安卓模擬器網路](https://developer.android.com/studio/run/emulator-networking.html)，AVD 似乎可以直接透過 `10.0.2.2` 存取 `localhost`，不過個人使用的雷電模擬器似乎無法

![](https://i.imgur.com/SUjhjrT.png)

由於透過 Node-RED 作為端點，這邊 `web` 的 `url` 需對應到 Node-Red 的 `http-in`，在此設定端點為 `/hello`：
```
http://192.168.10.105:1880/hello
```
![](https://i.imgur.com/9qRbxAs.png)

`POST` 文字需要加上屬性，在此為 `name`，以便後續 parse
```python=
if btn_request.Click:
    if TextBox1.Text not null:
        Web1.PostText(text=("name="+TextBox1.Text))

if Web1.GotText:
    Notifier1.ShowMessageDialog(message=responseContent,
                                title="Response",
                                buttonText="Ok")
```
![](https://i.imgur.com/7Iyi0W4.png)

Node-RED 拉出以下所示
* `http-in`：Method 選擇 `POST`，URL 選擇 `/hello`，對應 `Web1`
* `function`：從 `http-in` 得到的內容為 JSON，透過 `Web1` 對應的屬性 `name` 來取得資訊
```
msg.payload = "Hi~ " + msg.payload.name
return msg;
```
* `http-response`：將 `msg` 打包回傳
* 前面的 `debug`：觀察送入的 `payload` JSON
* 後面的 `debug`：檢查回傳的訊息

![](https://i.imgur.com/qfG0ys8.png)

### 實際畫面
![](https://i.imgur.com/gRJDfBw.png)
