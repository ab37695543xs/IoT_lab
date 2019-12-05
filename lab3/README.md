# 物聯網環境建置與測試
物聯網目錄：
* 本篇 (Lab3)
* [物聯網 Lab1](/jV8TXnQKREeo2GTfujuyRQ)
* [物聯網 Lab2](/I4zHw3uDTneV2Jw3G829kQ)
* [物聯網 Lab4](/Tm65KQpQR0aUrsrUTlGgzQ)
* [物聯網 Lab5](/pZzuShDZQ1qnUSOQ9RTgYg)

## OM2M 建置
### 安裝 Oracle JDK 8
目前 Java LTS 有 Java 8 和 Java 11，但在此先以 Java 8 為主

[官網](https://www.oracle.com/java/technologies/jdk8-downloads.html)下載 JDK 會需要申請帳號
可至[阿榮福利味](https://www.azofreeware.com/2013/11/java-development-kit-jdk-7-update-45.html)下載最新 JDK 8
> 解壓過程 win10 如果出現風險的警告，直接選仍要執行

![](https://i.imgur.com/DX6X41f.jpg)

如果自己選擇路徑的話，JDK 與 JRE 最好放在一起，個人放在 `C:\Java` 底下兩個資料夾，也可以都預設，自己清楚就好
![](https://i.imgur.com/4vdHXEH.png)
![](https://i.imgur.com/PCigknM.png)

打開 `控制台/系統與安全性/系統/進階系統設定/環境變數`，新增系統變數 `JAVA_HOME`
![](https://i.imgur.com/md9sqcK.png)

再修改系統變數 `Path`
![](https://i.imgur.com/XML3QbT.png)

最後在 cmd 執行 `javac` 測試

### 安裝 Apache Maven
為 Java 的專案管理及自動構建工具

[官網](https://maven.apache.org/download.cgi)下載 zip 檔
![](https://i.imgur.com/E5cRHET.jpg)

解壓縮後記錄檔案路徑，個人是和上面 JDK 等放在一起 `C:Java\apache-maven-3.6.2\bin`
，一樣打開 `控制台\系統與安全性\系統\進階系統設定\環境變數`，修改使用者變數中的 `Path`
![](https://i.imgur.com/K2PbVrt.png)

如果已安裝 JDK，執行 `mvn --version` 應會出現如下所示
![](https://i.imgur.com/5jNchow.png)


### 安裝 OM2M
先選好路徑，個人一樣選在 C，在 cmd 執行以下指令
> OM2M 有兩種版本： [ONE 版本](https://wiki.eclipse.org/OM2M/one/Clone)與 [SMART 版本](https://wiki.eclipse.org/OM2M/Clone)，請使用 SMART
```
git clone -b smart https://git.eclipse.org/r/om2m/org.eclipse.om2m
```
切換至 OM2M 資料夾 `org.eclipse.om2m`
```
cd org.eclipse.om2m
```
安裝 OM2M
> 如果出現 `Unable to locate the Javac Compiler`，請回去檢查 `JAVA_HOME` 與 `bin` 有沒有設置好
```
mvn clean install
```
![](https://i.imgur.com/5b9LlB1.png)


server (NSCL) 的執行檔：
```
"C:\org.eclipse.om2m\org.eclipse.om2m.site.nscl\target\products\nscl\win32\win32\x86_64\start.bat"
```
gateway (GSCL) 的執行檔：
```
"C:\org.eclipse.om2m\org.eclipse.om2m.site.gscl\target\products\gscl\win32\win32\x86_64\start.bat"
```
可以各自建立捷徑至桌面並重新命名，方便以後快速啟動

先啟動 server 再啟動 gateway，若先啟動 gateway，則會持續發送授權請求

預設位址：
* server (NSCL)：位址為 `127.0.0.1:8181` 或 `localhost:8181`
* gateway (GSCL)：位址為 `127.0.0.1:8080` 或 `localhost:8080`

帳號密碼：
* 管理者：admin/admin
* 訪客：guest/guest

> 以上設定均可參考 [OM2M/Configuration](https://wiki.eclipse.org/OM2M/Configuration) 修改

基本上都會透過 GSCL (8181) 操作
![](https://i.imgur.com/vUk1wyy.png)


## Postman 與 Node-RED 建置
[Postman 官網](https://www.getpostman.com/)下載安裝檔
![](https://i.imgur.com/nrJZEmy.jpg)

[Node.js 官網](https://nodejs.org/en/)下載安裝檔

個人一樣安裝在 C，過程中 necessary tools 可以不用勾選
![](https://i.imgur.com/BQ8ERyt.png)

在 cmd 執行 `node --version && npm --version` 測試後，依照 [Node-RED 官方指示](https://nodered.org/docs/getting-started/windows)，執行以下指令
```
npm install -g --unsafe-perm node-red
```
最後在 cmd 執行 `node-red`，並在瀏覽器打開 `localhost:1880` 即會出現以下畫面
![](https://i.imgur.com/LCkArjx.png)

## Postman 實作
:::info
注意每個請求都需要 `Basic Auth`，帳密使用 `admin`
注意使用哪種 RESTful API
可參考 [OM2M RESTful API](https://wiki.eclipse.org/OM2M/REST_API)
:::
### 建立環境
進入畫面後先在右上 `No Environment` 右邊點擊齒輪，點擊 add 新增一個環境，這邊設定一個 `IoT` 環境
* `gscl`：`http://localhost:8181/om2m/gscl`
* `node-red`：`http://localhost:1880`

![](https://i.imgur.com/r2AoyPQ.png)

回到主畫面後右上角選擇 `IoT` 環境，透過 `{{變數名稱}}` 即可使用變數

### 連線測試
```
GET, {{gscl}}
```
![](https://i.imgur.com/Oi2y3ST.png)


得到 `body`
```xml=
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<om2m:sclBase xmlns:om2m="http://uri.etsi.org/m2m" xmlns:xmime="http://www.w3.org/2005/05/xmlmime">
    <om2m:accessRightID>gscl/accessRights/AR_ADMIN</om2m:accessRightID>
    <om2m:searchStrings>
        <om2m:searchString>ResourceType/SclBase</om2m:searchString>
        <om2m:searchString>ResourceID/gscl</om2m:searchString>
    </om2m:searchStrings>
    <om2m:creationTime>2019-11-26T12:30:37.468+08:00</om2m:creationTime>
    <om2m:lastModifiedTime>2019-11-26T12:30:37.470+08:00</om2m:lastModifiedTime>
    <om2m:sclsReference>gscl/scls</om2m:sclsReference>
    <om2m:applicationsReference>gscl/applications</om2m:applicationsReference>
    <om2m:containersReference>gscl/containers</om2m:containersReference>
    <om2m:groupsReference>gscl/groups</om2m:groupsReference>
    <om2m:accessRightsReference>gscl/accessRights</om2m:accessRightsReference>
    <om2m:subscriptionsReference>gscl/subscriptions</om2m:subscriptionsReference>
    <om2m:discoveryReference>gscl/discovery</om2m:discoveryReference>
</om2m:sclBase>
```

### 建立應用 (MY_SENSOR)
```
POST, {{gscl}}/applications
```
修改 `body`，選擇 `raw` 與 `XML`，建立名稱為 `MY_SENSOR` 的應用，包含三個 `searchString`
```xml=
<om2m:application xmlns:om2m="http://uri.etsi.org/m2m" appId="MY_SENSOR">
    <om2m:searchStrings>
    	<om2m:searchString>Type/sensor</om2m:searchString>
    	<om2m:searchString>Category/temperature</om2m:searchString>
    	<om2m:searchString>Location/Home</om2m:searchString>
    </om2m:searchStrings>
</om2m:application>
```
![](https://i.imgur.com/vooHeG6.png)

查看 GSCL 網頁，可以看到 `applicationCollection` 下新增了 `MY_SENSOR`，以及對應的 `searchString` 
> 可以看到 Logout 按鈕下面有右邊元件的位址，善用這個可以避免路徑錯誤

![](https://i.imgur.com/GQbp3PS.png)

### 建立容器 (DATA)
```
POST, {{gscl}}/applications/MY_SENSOR/containers
```
修改 `body`，在 `MY_SENSOR` 下建立名稱為 `DATA` 的容器
```xml=
<om2m:container xmlns:om2m="http://uri.etsi.org/m2m" om2m:id="DATA">
</om2m:container>
```
![](https://i.imgur.com/tKMrY9q.png)
![](https://i.imgur.com/TBARMRW.png)

### 建立物件 (DATA)
```
POST, {{gscl}}/applications/MY_SENSOR/containers/DATA/contentInstances
```
修改 `body`，建立一個 sensor 資料
> 注意這邊都要用 `obj` 打包
```xml=
<obj>
    <str name="appId" val="MY_SENSOR"/>
    <str name="category" val="temperature"/>
    <str name="data" val="27"/>
    <str name="unit" val="celsius"/>
</obj>
```
![](https://i.imgur.com/q8dj37T.png)
![](https://i.imgur.com/lJdXMuY.png)


### 建立訂閱 (DATA)
```
POST, {{gscl}}/applications/MY_SENSOR/containers/DATA/contentInstances/subscriptions
```
修改 `body`，在 `DATA` 的 `contentInstaces` 底下建立一個訂閱，如果有新的資料加入，就會告知 `contact`，在此設為 `http://localhost:1400/monitor`
```xml=
<om2m:subscription xmlns:om2m="http://uri.etsi.org/m2m">
    <om2m:contact>http://localhost:1400/monitor</om2m:contact>
</om2m:subscription>
```
![](https://i.imgur.com/TR8dtqN.png)
![](https://i.imgur.com/ftDdDL9.png)


這邊監視器使用官方提供的範例，下載 [Monitor-bin](https://www.dropbox.com/s/yjvuf7hnn5q83md/monitor-bin.zip?dl=0) 後解壓縮，執行 `start.bat`
> 監聽的埠可以從 `config` 修改

依照上面新增物件 (DATA) 的指示再做一次，應該會顯示如下通知
> 沒反應的話可以點一下 cmd 或按一下 `Enter`

![](https://i.imgur.com/iUevwCv.png)


### 刪除元件
即 `DELETE` 加上對應元件之位址，刪除上層的會連下層的一起不見

* 刪除 `DATA`
```
DELETE, {{gscl}}/applications/MY_SENSOR/containers/DATA
```
* 刪除 `MY_SENSOR`
```
DELETE, {{gscl}}/applications/MY_SENSOR
```

## Node-RED 實作
:::info
在 cmd 執行 `node-red`，並在瀏覽器打開 `localhost:1880` 即可開啟 Node-RED
記得每次操作完記得按 `Deploy` 部署
注意每個請求都需要 `Basic Authentication`，帳密使用 `admin`
注意使用哪種 RESTful API
可參考 [OM2M RESTful API](https://wiki.eclipse.org/OM2M/REST_API)
:::
### 連線測試
先從左邊工具拉出 `inject`、`http request` 與 `debug`
![](https://i.imgur.com/xbhSBr1.png)

修改 `http request`
> `Name` 都可以調整成自己習慣的名稱
> 後續 `http request` 皆以此類推，修改路徑與授權
```
GET, http://localhost:8181/om2m/gscl
```
![](https://i.imgur.com/KO5jq7F.png)

透過 timestamp 的按鈕，可以在右邊偵錯訊息看到與 Postman 一樣的內容
![](https://i.imgur.com/GraoZAM.png)


### 建立應用 (MY_SENSOR)
加入 `function` `MY_SENSOR` 形成如下所示
![](https://i.imgur.com/3aPFsd2.png)

一樣去修改 `http request` 位址
```
POST, http://localhost:8181/om2m/gscl/applications
```
修改 `function` 中 `payload` 的部分，等同前面的 `body`
> 後續皆大同小異，可以不參考排版，注意結尾分號
```javascript=
msg.payload =
'<om2m:application xmlns:om2m="http://uri.etsi.org/m2m" appId="MY_SENSOR">'+
    '<om2m:searchStrings>'+
        '<om2m:searchString>Type/sensor</om2m:searchString>'+
        '<om2m:searchString>Category/temperature</om2m:searchString>'+
        '<om2m:searchString>Location/Home</om2m:searchString>'+
    '</om2m:searchStrings>'+
'</om2m:application>';
return msg;
```
### 建立容器 (DATA)
![](https://i.imgur.com/eeut3Bj.png)
```
POST, http://localhost:8181/om2m/gscl/applications/MY_SENSOR/containers
```
```javascript=
msg.payload =
'<om2m:container xmlns:om2m="http://uri.etsi.org/m2m" om2m:id="DATA">'+
'</om2m:container>';
return msg;
```

### 建立物件 (DATA)
![](https://i.imgur.com/aTvI1Ab.png)
```
POST, http://localhost:8181/om2m/gscl/applications/MY_SENSOR/containers/DATA/contentInstances
```
```javascript=
msg.payload =
'<obj>'+
    '<str name="appId" val="MY_SENSOR"/>'+
    '<str name="category" val="temperature"/>'+
    '<str name="data" val="27"/>'+
    '<str name="unit" val="celsius"/>'+
'</obj>'
return msg;
```

### 建立訂閱 (DATA)
![](https://i.imgur.com/pVkCDhY.png)
```
POST, http://localhost:8181/om2m/gscl/applications/MY_SENSOR/containers/DATA/contentInstances/subscriptions
```
```javascript=
msg.payload=
'<om2m:subscription xmlns:om2m="http://uri.etsi.org/m2m">'+ 
    '<om2m:contact>http://localhost:1400/monitor</om2m:contact>'+
'</om2m:subscription>';
return msg;
```
如果我們想用 Node-RED 直接來訂閱，先將上面 `contact` 部分改成 `http://localhost:1880/SUB`，表示會通知 Node-RED 下的 `/SUB`

拉個 `http-in` 與 `debug`，`Method` 修改為 `POST`，路徑對應上面的 `/SUB`，表示傳到 `http://localhost:1880/SUB` 的請求都會跑到這
> `http-in` 通常連帶 `http_response`，不過這邊因為是由 OM2M 通知，所以就不特地回傳了

![](https://i.imgur.com/fogxohj.png)

再新建一次物件 (DATA) 後應該能看到兩個偵錯訊息，一個來自新建時的，一個來自監聽的
> 有多條偵錯訊息時，滑鼠移動到其中一個訊息，左邊對應的 `debug` 元件會顯示紅框

![](https://i.imgur.com/DWU3ZqN.png)

### 刪除元件
與連線測試相似，只是改成 `DELETE`
![](https://i.imgur.com/7NUa2cC.png)

* 刪除 `DATA`
```
DELETE, http://localhost:8181/om2m/gscl/applications/MY_SENSOR/containers/DATA
```
* 刪除 `MY_SENSOR`
```
DELETE, http://localhost:8181/om2m/gscl/applications/MY_SENSOR
```

## 綜合實作
**情境一流程：**
1. 由 Postman 模擬字串處理受限的軟體，POST 給 Node-RED 純文字訊息
2. 由 Node-RED 進行字串處理，POST 給 OM2M 建立該物件

![](https://i.imgur.com/CupB9nS.png)


Postman `body`，故意設為純文字，傳至 Node-RED 的 `/INFO`
> 中間分隔字元自己方便處理就好

![](https://i.imgur.com/fibmbiu.png)

Node-RED `function`，一樣以 `obj` 格式送給 OM2M
> `split()` 分割字串後會存入陣列
```javascript=
var data = msg.payload.split(",");
var name = data[0];
var height = data[1];
var weight = data[2];

msg.payload =
'<obj>'+
    '<str name="name" val='+ name +'/>'+
    '<str name="height" val='+ height +'/>'+
    '<str name="weight" val='+ weight +'/>'+
'</obj>';

return msg;
```
![](https://i.imgur.com/7APrKqi.png)

**情境二流程：**
1. 先於 OM2M 建立訂閱
2. 由 Node-RED (或 Postman) 於 OM2M 上建立物件
3. 由 Node-RED 接收訂閱通知，並處理收到的訊息
4. 將處理過後的訊息另外寫檔記錄

首先依照之前訂閱 (DATA) 的教學建立訂閱，這邊將情境一由 Postman 建立物件的部分簡化給 Node-RED，同時先將 `debug` 先取消
> 如果之前還有 flow 的 `http-in` 有同樣路徑的，記得先刪掉，否則會跑到那邊

可以看到一有新的資料建立後，訂閱的 `/SUB` 馬上就收到訊息，debug1 顯示的是完整通知的 XML，debug2 經由 parser 得到結構化的資訊，而我們需要的物件資訊就在底下這個位置
> 可以在 parser 的偵錯訊息旁直接點擊圖示複製路徑，記得加上 `msg.`
```javascript
msg.payload["om2m:notify"]["om2m:representation"][0]._
```
![](https://i.imgur.com/zl1okRu.png)


加入 `function` 進行編碼轉換，可以看到原本只是通知的 XML，裏頭拆出來的 XML 其實就是建立物件 OM2M 會回傳的訊息
> 編碼配合 XML 與 Node-RED，統一使用 `UTF-8`
```javascript=
var text = payload["om2m:notify"]["om2m:representation"][0]._;
var data = new Buffer(text, 'base64').toString('utf8');
msg.payload = data;

return msg;
```
![](https://i.imgur.com/nuyKbjO.png)


同樣類似方式加入 `function`，得到的正是我們新建物件時送下去的內容
```javascript=
var text = msg.payload["om2m:contentInstance"]["om2m:content"][0]._;
var data = new Buffer(text, 'base64').toString('utf8');
msg.payload = data;

return msg;
```
![](https://i.imgur.com/Ou3Z5Pu.png)

加入 `file`，如同設定下面的提示，這邊由於沒有用絕對路徑，基本上會預設寫到 `%UserProfile%` 這個位置，即使用者目錄下
![](https://i.imgur.com/JdxVfiL.png)
![](https://i.imgur.com/4qMPLJw.png)
