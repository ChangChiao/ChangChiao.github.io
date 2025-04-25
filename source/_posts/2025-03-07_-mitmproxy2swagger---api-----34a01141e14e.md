---
title: 用mitmproxy2swagger來實現api逆向工程
description: >-
  不曉得大家是否遇過這樣的情況：接手一個新專案時，卻發現沒有 API 文件，導致無法全面了解 API 的結構，包括 URI、請求 (payload) 及回應
  (response)。這種情況下，唯一的解法往往是透過操作網頁，打開開發者工具 DevTools 來觀察 Network…
date: "2025-03-07T01:28:37.336Z"
categories: "proxy"
keywords: "proxy, mitmproxy, mitmproxy2swagger"
---

![](/img/1__Y7ec4CcZh0i8hmU1dFvqng.jpeg)

不曉得大家是否遇過這樣的情況：接手一個新專案時，卻發現沒有 API 文件，導致無法全面了解 API 的結構，包括 URI、請求 (payload) 及回應 (response)。這種情況下，唯一的解法往往是透過操作網頁，打開開發者工具 DevTools 來觀察 Network 請求來了解 api 的全貌，但無論對前端還是後端工程師來說，這都會是一件相當痛苦的事。

此時，我們可以利用 mitmproxy 和**mitmproxy2swagger** 來實現 API 逆向工程。mitmproxy 作為代理伺服器，透過操作網頁就能自動捕捉網路封包，偵測所有被觸發的 API 並且生成 flow 檔案，包含請求的 api url、payload、response 等等，省去手動分析的麻煩。而 mitmproxy2swagger 則是將 flow 檔案轉換成 open api YAML，生成 API 文件

![](/img/1__23VvDLZL7bnqXA____SqM__xA.png)

作者用 mac 開發，所以會使用 brew 來安裝 mitmproxy

```bash
brew install mitmproxy
```

安裝好之後可以選擇用 command line 的方式或是 web GUI 的方式來操作**mitmproxy**，為了更直觀的展示 mitmproxy 的功能，這裡選擇用 web GUI 的方式，執行以下指令

```bash
mitmweb
```

執行成功後，網頁會自動打開 127.0.0.1:8081

![](/img/1__xclU9A1SglTX6fP7cwRoyw.png)

在 capture 的頁籤裡建議可以設定 local applications，因為 proxy 會攔截的網路封包不僅僅只是網頁的而已，還包含電腦上其他應用程式，ex. line、outlook、discord，任何在電腦上會連上網的應用程式的網路封包都會被追蹤，因此在查看 log 會有雜訊過多的問題，因此建議設定為要監看的目標即可 ，作者的目的是要監看網頁的封包，因此設定為 google chrome

![](/img/1__kAX9N2XBDbkFRAMjlS7XqQ.png)

在 MAC 系統設定裡開啟代理伺服器的設定，打開 http 和 https 的 proxy，並且將伺服器和連接埠設定為 mitmproxy 預設的 127.0.0.1 和 8080

![](/img/1__gI5O8C0KVOlx7DFOYYCXYQ.png)

這個時候會發現所有的網頁都變成不安全連線了，

![](/img/1__xULjLWIyPw__VLUgCE2y5vg.png)

以往在沒有代理伺服器的情況下，瀏覽器會和 web server 建立 SSL/TLS 安全連線(https)，但現在因為我們開啟了代理伺服器，瀏覽器和 server 之間多了一個中介者，瀏覽器認為代理伺服器沒有安全憑證所以認定為不安全連線，所以我們需要在自己的電腦上安裝 proxy 的憑證

![](/img/1__z07x1Ug42O3iXThzYMnefg.png)

在網址列輸入 mitm.it 會看到以下畫面，再根據不同的平台下載對應的憑證

![](/img/1__bCtpbWjVyEgmvkC7oWgQBQ.png)

下載 mitmproxy 的憑證之後，點兩下就會自動安裝，憑證預設為不受信任

![](/img/1__ZyI7XQSKBdfIzzshaEU01Q.png)

需要手動將憑證改為永遠信任

![](/img/1__HXtfNkR6Xc1SbnXNmwlTbA.png)

安裝完 mitmproxy 憑證後，就可以正常瀏覽網頁了

以政府的公開 api 平台為例[https://data.gov.tw/](https://data.gov.tw/)，進到網頁後任意地點選幾個頁面再回到 127.0.0.1:8081 查看，會發現剛剛所有瀏覽的紀錄都會被記錄起來

![](/img/1__6lLg1dmd__O__0MLiiLtH8Kg.png)

為了確保資料來源正確，我們可以用 search 的功能來篩選指定網域的內容

![](/img/1__qCJ7kmonDYKB2kB8xZsFuQ.png)

按下 save filtered 就會針對剛剛篩選的內容，自動下載 flow 檔

![](/img/1__MdzwOIqtLdmyw4RvoXapDw.png)

接下來換 mitmproxy2swagger 接手，先安裝 mitmproxy2swagger，若不想安裝 python，也可以使用官方提供 docker image 來建立虛擬環境，省去安裝 python 的麻煩

#python3 執行 pip3 指令，若版本低於 python3 則執行 pip 指令

```bash
pip3 install mitmproxy2swagger
```

接著執行指令將剛剛的 flows 檔案輸出為 api.yml，-i 後面帶輸入的 flow 檔名，-o 後面帶要輸出的檔名，-p 可以設定 api 的 url

```bash
mitmproxy2swagger -i flows -o api.yaml -p https://data.gov.tw/api
```

假如有看到彩虹進度條就代表生成成功了！

![](/img/1__d__rXRTrAYEDrmlM2CgLDWQ.png)

但其實我第一次執行指令會發生 TypeError: ‘int’ object is not subscriptable 的錯誤

![](/img/1__V6ZpS0yL2e__65JP1kCp3Ig.png)

爬了一下 github 的 issue 討論區，找到了[相關的討論](https://github.com/alufers/mitmproxy2swagger/issues/95)，將指令改為以下就成功了，但不確定原因為何

```bash
mitmproxy2swagger -i flows -o api.yaml -p https://data.gov.tw/api -f flow
```

打開剛剛生成的 yml，會發現已經有了初版的架構

```yml
openapi: 3.0.0
info:
 title: flows Mitmproxy2Swagger
 version: 1.0.0
servers:
\- url: https://data.gov.tw/api
 description: The default server
paths: {}
x-path-templates:
\# Remove the ignore: prefix to generate an endpoint with its URL
\# Lines that are closer to the top take precedence, the matching is greedy
\- ignore:/front/comment/detail
\- ignore:/front/dataset/changed/list
\- ignore:/front/dataset/list
\- ignore:/front/dataset/options-list
\- ignore:/front/taxonomy/popular-keywords/list

這時可以篩選哪些 api uri 要生成 yml，哪些不用，假設要生成 yml，可以把對應的 api 前面的「ignore：」移除

openapi: 3.0.0
info:
 title: flows Mitmproxy2Swagger
 version: 1.0.0
servers:
 \- url: https://data.gov.tw/api
 description: The default server
paths: {}
x-path-templates:
 \# Remove the ignore: prefix to generate an endpoint with its URL
 \# Lines that are closer to the top take precedence, the matching is greedy
 \- /front/comment/detail
 \- /front/dataset/changed/list
 \- /front/dataset/list
 \- /front/dataset/options-list
 \- /front/taxonomy/popular-keywords/list
```

再執行一次剛剛的指令

mitmproxy2swagger -i flows -o api.yaml -p https://data.gov.tw/api

就能夠生成完整的 api yml，以下只擷取片段內容

```yml
openapi: 3.0.0
info:
 title: flows Mitmproxy2Swagger
 version: 1.0.0
servers:
\- url: https://data.gov.tw/api
 description: The default server
paths:
 /front/comment/detail:
 post:
 summary: POST detail
 responses:
 '200':
 description: ''
 content:
 application/json:
 schema:
 type: object
 properties:
 success:
 type: boolean
 code:
 type: number
 message:
 type: string
 payload:
 type: object
 properties:
 results:
 type: array
 items: {}
 requestBody:
 content:
 application/json:
 schema:
 type: object
 properties:
 nid:
 type: string
```

把這份 yml 貼到 [swagger editor](https://editor.swagger.io/)，讓我們可以更直觀的閱讀，api 的文件就大功告成啦！

![](/img/1________S67dklZIAtaVbYMKZAg.png)

#### 結語

個人認為 api 逆向工程僅適用於一開始就沒有寫 api 文件的專案，偏向補救用途，畢竟自動生成的 api 文件還是會有以下缺點

1.  仰賴使用者操作網頁來生成，如果有些流程沒有操作到，則對應的 api 並不會生成，導致文件不完整
2.  API 文件通常依據返回的資料類型進行推斷，例如數值 `0、1、2` 可能會被判定為數字類型。然而，在實際應用中，這類資料可能是枚舉值（例如：`0 = 未開始，1 = 處理中，2 = 已完成`）。這類型別資訊無法僅依賴工具推斷，而需要開發者自行定義
3.  若後端自訂義了多種錯誤回應，這些錯誤訊息無法在自動生成的文件中展現，使得 API 使用者無法事先掌握可能的錯誤狀況。

因此，是否使用 API 逆向工程應視專案需求而定，並非所有情境都適合。
