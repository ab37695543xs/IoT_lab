# 物聯網 Lab4
物聯網目錄：
* [物聯網環境建置與測試](/9cAzkUsBRZCozYU6PdEXDA)
* [物聯網 Lab1](/jV8TXnQKREeo2GTfujuyRQ)
* [物聯網 Lab2](/I4zHw3uDTneV2Jw3G829kQ)
* 本篇 (Lab4)
* [物聯網 Lab5](/pZzuShDZQ1qnUSOQ9RTgYg)

## 顯示地圖於 google ver.2
### 達成目標
基本功能：
- [x] 傳送自己位置藉由 Node-RED 給 OM2M
- [x] Node-RED 訂閱並處理資料與存放 (寫檔)
- [x] 從 Node-RED 獲取目前資料 (讀檔)
- [x] 顯示 google 地圖

額外功能：
- [x] 傳送位置時改用 `notifier` 顯示狀況
- [x] 延續 Lab2，直接用兩個 `web` 模擬兩支手機

### 版面設計
<p float="left">
  <img src="https://i.imgur.com/d9AXXmj.png" width="50%" />
  <img src="https://i.imgur.com/rVXqHmF.png" width="40%" /> 
</p>

大體類似 Lab2，一樣先幫這幾個元件填入初始值
* `web_send`：`Url` 填入 `http://電腦的 IPv4:1880/GPS`
* `web_get`：`Url` 填入 `http://電腦的 IPv4:1880/INFO`
* `ActivityStarter1`：`Activity` 填入 ` android.intent.action.VIEW`

### 程式設計

App inventer 變動不大，`Get` 一樣是以 `,` 來分割收到的資訊，同時把變數的使用降低
![](https://i.imgur.com/SH4vhBF.png)

Node-RED 部分先拉出以下所示
* `Data_sub`：設定 `DATA` 如果有新物件，會通知 `/SUB`
* `/GPS`：接收 App 傳來的位置資訊，加到 `DATA` 中
* `/SUB`：被通知時進行字串處理，最終將 `obj` 部分擷取出來寫檔 (相對路徑為使用者目錄下)
* `/INFO`：App `Get` 時，讀檔並字串處理，回傳以 `,` 分隔的位置資訊

![](https://i.imgur.com/Cyfv9Go.png)

`/SUB` 字串處理的部分可以參考[物聯網環境建置與測試](/9cAzkUsBRZCozYU6PdEXDA)最後面
> 多善用 `debug` 來看路徑

`/INFO` 字串處理只需要擷取經緯度如下，App 即可用 `,` 分割
```javascript=
var lat = msg.payload.obj.str[0].$.val;
var lon = msg.payload.obj.str[1].$.val;

msg.payload = lat + "," + lon;

return msg;
```

### 實際畫面
先模擬定位至安平古堡
![](https://i.imgur.com/udMdfqT.png)

傳送給 Server
![](https://i.imgur.com/fInF5mp.png)

從 Server 獲取
![](https://i.imgur.com/4kgP6Hr.png)


