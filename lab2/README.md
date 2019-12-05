# 物聯網 Lab2
物聯網目錄：
* [物聯網環境建置](/9cAzkUsBRZCozYU6PdEXDA)
* [物聯網 Lab1](/jV8TXnQKREeo2GTfujuyRQ)
* 本篇 (Lab2)
* [物聯網 Lab4](/Tm65KQpQR0aUrsrUTlGgzQ)
* [物聯網 Lab5](/pZzuShDZQ1qnUSOQ9RTgYg)

## 感測器資料
### 達成目標
基本功能：
- [x] 加速度儀資料
- [x] 姿勢儀資料
- [x] 位置資料

### 版面設計
<p float="left">
  <img src="https://i.imgur.com/fKEobzZ.png" width="50%" />
  <img src="https://i.imgur.com/8W9g8fR.png" width="40%" /> 
</p>

### 程式設計
```python=
if btn_get.Click:
    lab_accel.Text = "Accelerometer:\n" +\
                    "X: " + AccelerometerSensor1.XAccel +\
                    "\nY: " + AccelerometerSensor1.YAccel +\
                    "\nZ: " + AccelerometerSensor1.ZAccel
    lab_ori.Text = "Orientation:\n" +\
                    "Roll: " + OrientationSensor1.Roll +\
                    "\nPitch: " + AccelerometerSensor1.Pitch +\
                    "\nAzimuth: " + AccelerometerSensor1.Azimuth
    lab_loc.Text = "Location:\n" +\
                    "[" + LocationSensor1.Latitude + "," + LocationSensor1.Longitude + "]"
```
![](https://i.imgur.com/z9Nf1Ny.png)

### 實際畫面
![](https://i.imgur.com/XfCuO1l.png)

## 顯示位置於 Google
### 達成目標
基本功能：
- [x] 傳送自己位置給 server (POST)
- [x] 顯示自己位置於 Google
- [x] 獲取 server 位置 (GET)
- [x] 顯示 server 位置於 Google

額外功能：
- [x] 顯示 server 回傳的經緯度

### 版面設計
<p float="left">
  <img src="https://i.imgur.com/7VcRcmr.png" width="50%" />
  <img src="https://i.imgur.com/iHEndx0.png" width="40%" /> 
</p>

與 Lab1 類似，這邊設定 `web_send` 端點為 `/MY_LOC`， `web_get` 端點為 `/SERVER_LOC`，前者負責 `POST`，後者負責 `GET`
```
http://電腦 IPv4:1880/MY_LOC
http://電腦 IPv4:1880/SERVER_LOC
```

### 程式設計

多屬性需要 `&` 來分隔，在此為兩個屬性 `lat=xxx&lon=xxx`
顯示地圖的部分可以參考 [android intent](https://developers.google.com/maps/documentation/urls/android-intents)，範例是使用經緯度搜尋，這邊使用經緯度加上縮放
獲取 server 位置需要 `split` 得到陣列，再分別記錄起來
> 須注意 list 是從 1 開始而非 0

```python=
float Lat_M = 0
float Lon_M = 0
float Lat_S = 0
float Lon_S = 0
string temp = ""

if btn_me.Click:
    Lat_M = LocationSensor1.Latitude
    Lon_M = LocationSensor1.Longitude
    lab_loc.Text = "Me: " + Lat_M + ", " + Lon_M
    web_send.PostText(text=("lat="+Lat_M+"&lon="+Lon_M))

if web_send.GotText:
    lab_loc.Text = "My Location: " + responseContent
    lab_info.Text = responseCode

if btn_showM.Clock:
    ActivityStarter1.Action = "android.intent.action.VIEW"
    ActivityStarter1.DataUri = "geo:" + Lat_M + "," + Lon_M + "?z=zoom"
    ActivityStarter1.StartActivity()

if btn_server.Click:
    web_get.Get()

if web_get.GotText:
    lab_loc.Text = "Server's Location: " + responseContent
    lab_info.Text = responseCode
    temp = split(responseContent, ",")
    Lat_S = temp[1]
    Lon_S = temp[2]

if btn_showM.Clock:
    ActivityStarter1.Action = "android.intent.action.VIEW"
    ActivityStarter1.DataUri = "geo:" + Lat_S + "," + Lon_S + "?z=zoom"
    ActivityStarter1.StartActivity()
```

傳送給 server 與顯示自己位置：
![](https://i.imgur.com/tLEDGfo.png)

獲取 server 位置與顯示：
![](https://i.imgur.com/IFFGqE4.png)

Node-RED 拉出以下所示
* `http-in`：分別為 `POST` 與 `GET`，也分別對應 `/MY_LOC` 與 `SERVER_LOC`
* `function` 的 `MY_LOC`：單純把傳給 server 的內容再打包回來
```
msg.payload = msg.payload.lat + ', ' + msg.payload.lon;
return msg;
```
* `function` 的 `SERVER_LOC`：固定回傳一個定點，在此設計為 `25.040288, 121.512160` (總統府)

![](https://i.imgur.com/mIB2J87.png)

### 實際畫面
自己位置
![](https://i.imgur.com/lfETpOX.png)
![](https://i.imgur.com/3ubYX88.png)

server 位置
![](https://i.imgur.com/RuRzlir.png)
![](https://i.imgur.com/xXLJLey.png)

