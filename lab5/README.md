# 物聯網 Lab5
物聯網目錄：
* [物聯網環境建置與測試](/9cAzkUsBRZCozYU6PdEXDA)
* [物聯網 Lab1](/jV8TXnQKREeo2GTfujuyRQ)
* [物聯網 Lab2](/I4zHw3uDTneV2Jw3G829kQ)
* [物聯網 Lab4](/Tm65KQpQR0aUrsrUTlGgzQ)
* 本篇 (Lab5)

## 遠端燈泡同步控制
### 前備知識
打開 NSCL 與 GSCL 後，在 GSCL 部分打入 `ss` 出現如下視窗，再輸入 `start 25` 開始燈泡模擬 `ipu.sample`

![](https://i.imgur.com/VFwQZ8x.png)

### 達成目標
基本功能：
- [x] 手機控制燈泡亮暗
- [x] 手機同步顯示燈泡資訊

額外功能：
- [x] 手動同步改成定時同步
- [x] 按鈕控制改成 `Switch`，且配合狀態同步顯示

### 版面設計
<p float="left">
  <img src="https://i.imgur.com/Xa25hwS.png" width="50%" />
  <img src="https://i.imgur.com/EBHgBmW.png" width="40%" /> 
</p>

一樣需注意元件初始值
* `send`：`Url` 填入 `http://電腦的 IPv4:1880/LAMP`
* `recv`：`Url` 填入 `http://電腦的 IPv4:1880/STATUS`
* `Clock1`：`TimeInterval` 填入 700 毫秒

### 程式設計
這次主要因為 `Switch` 同步的關係，在程式上需要考慮的點會比較多
* `recv` 主要是進行定期同步，根據 OM2M 紀錄更新 APP 狀態，同時也更新 `Switch`
* 兩個變數 `lamp0` 與 `lamp1` 為紀錄下一輪同步前的狀態
* 由於 OM2M 端切換燈泡時會使得 `Switch` 一起改變，而切換又會造成事件觸發，所以需要比較同步前的狀態是否有更動，如果不同就表示是 APP 主動切換燈泡
* 控制全部按鈕的部分比較複雜，同步基本上就是與電路的 AND 同理，在事件部分比對的則是兩個 `Switch` 目前狀況，不同就表示主動控制全部
* 傳送控制指令以 `control` 為主要關鍵字

![](https://i.imgur.com/qVklbkA.png)

Node-RED 部分拉出以下所示
* `/LAMP` 接收 APP 的控制訊息，根據數字執行對應的 OM2M 操作
* `/STATUS` 接收 APP 的同步訊息，先依序獲取燈泡最新狀態，再擷取對應的 true/false，最終合併傳回

![](https://i.imgur.com/SwmgBoM.png)

`CONTROL` 這邊使用 `status` 變數作為 `switch` 判斷，且使 `payload` 為空，以此傳送空指令
```javascript=
msg.status = msg.payload.control;
msg.payload = "";

return msg;
```

這邊的路徑與 OM2M 上的捷徑一致，ALL 的部分與其他的稍為不同
> 前面的號碼表示 `control` 所對應的指令

* (0) LAMP0 ON：`http://localhost:8181/om2m/gscl/applications/LAMP_0/lamps/true`
* (1) LAMP0 OFF：`http://localhost:8181/om2m/gscl/applications/LAMP_0/lamps/false`
* (2) LAMP1 ON：`http://localhost:8181/om2m/gscl/applications/LAMP_1/lamps/true`
* (3) LAMP1 OFF：`http://localhost:8181/om2m/gscl/applications/LAMP_1/lamps/false`
* (4) ALL ON：`http://localhost:8181/om2m/gscl/groups/ON_ALL/membersContent`
* (5) ALL FF：`http://localhost:8181/om2m/gscl/groups/OFF_ALL/membersContent`
* LAMP0 STAT：
`http://localhost:8181/om2m/gscl/applications/LAMP_0/containers/DATA/contentInstances/latest/content`
* LAMP1 STAT：
`http://localhost:8181/om2m/gscl/applications/LAMP_1/containers/DATA/contentInstances/latest/content`


`EXTRACT` 是一樣的 `function`，主要為了取得燈泡最新狀態的 true/false

```javascript=
msg.payload = msg.payload.obj.bool[0].$.val;

return msg;
```

`join` 這邊使用 `,` 分隔，且設定接受到兩個訊息後自動合併
> 需自行注意燈泡誰前誰後

![](https://i.imgur.com/vc54a7J.png)

### 實際畫面
![](https://i.imgur.com/V0n9AUi.png)

![](https://i.imgur.com/akJibVw.png)
