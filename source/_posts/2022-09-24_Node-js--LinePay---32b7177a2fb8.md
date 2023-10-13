---
title: Node.js串接LinePay筆記
description: 本次練習使用的技術為 —  前端：React，後端 ：Node.js + MongoDB，串接的LinePay版本為V3
date: "2022-09-24T02:56:13.898Z"
categories: NodeJs
tag: LinePay
keywords: []
---

![](/img/1__J0CNjMiLimCyDzOt5DWJiA.png)

_本次練習使用的技術為  —  前端：React，後端 ：Node.js + MongoDB，串接的 LinePay 版本為 V3_

近期在做 side Project 練習串接 LinePay，順手將過程紀錄下來，這篇文章會著重在 LinePay 串接的流程，Node js 和 MongoDB 的部分只會簡單帶過，因為本人也是後端初學者，文章裡的內容有些是自己摸索出來的，未必完全正確，如果有發現錯誤，歡迎留言告知我，萬分感謝!

讓我們先來看看 LinePay 官網提供的 PC 版流程圖吧!

![](/img/1__9iXmOBSZ0Vme4LDo__ffmEQ.png)

**PC 付款頁面-付款完成**

1.  在 LINE App 的付款頁面，LINE Pay 用戶選擇付款方式並輸入密碼。
2.  在選擇付款方式後，點擊付款按鈕啟用付款
3.  LINE Pay 用戶在 LINE App 確認付款資訊。
4.  在 PC 環境的等待付款頁面一旦收到用戶付款狀態成功，將會轉導至商家系統的“confirmUrl”。
5.  商家系統透過呼叫 Confirm API 來完成交易。

行動版的付款流程有些許差異，可以參考[這裡](https://pay.line.me/jp/developers/apis/onlineApis?locale=zh_TW)

#### 串接 LinePay 的前置作業

1.  先建立 sandBox 帳號，👉 這裡[申請](https://pay.line.me/tw/developers/techsupport/sandbox/testflow?locale=zh_TW)

![](/img/1__qjAJR3khkhaXPiLvTGx__TA.png)

2\. 註冊成功後會收到信件，裡面會有帳號密碼的資訊

![](/img/1__zqcPaem__OGpZFHR4HJyflw.jpeg)

#### 取得 Channel ID & Secret Key

申請好帳號之後登入[管理](https://pay.line.me/portal/tw/auth/login)平台，點開選單內的管理連結金鑰的頁面，按下查詢，會寄送驗證碼到你的電子信箱，驗證成功即可取得金鑰

![](/img/1__3IdY5uizhhrrL92fezkisw.png)

有了金鑰我們才能夠串接 LinaPay 的 API，為了避免金鑰外洩，因此會把金鑰存放在 env，至於 LinaPay 的版號和 api url 是否要放在 env，就看大家的開發習慣了

#### 白名單設置

為了提高安全性，必須將我們的後端 server IP 設為白名單，防範來路不明的請求(不過測試環境貌似可以不用設定，API 也能打得過

![](/img/1__cJMiMj1c4oWX8dmtHBNi7w.jpeg)

#### 建立一筆新訂單

![](/img/1__ftS916Luao__gAnUQfCV1tw.png)

點選購買的按鈕之後，前端會用 POST 的方式呼叫後端 /linepay 這支 API，後端收到請求後會先在資料庫建立一筆訂單，再呼叫 LinePay 的 API 請求付款網址

LinePay 要求的訂單資訊如下(request body)

![](/img/1__bKTY1__RS0dcf9wrLrUQuyQ.png)

本次的練習是模擬使用者購買訂閱 VIP 的服務，因此商品內容和價錢都會由後端這邊固定寫死，依照 LinePay 規定的格式建立的訂單物件如下

不過除了 LinePay 本身要求的資料格式之外，還要滿足資料庫要求的必填欄位，像是訂單編號 MerchantOrderNo，購買者 user 等等…， （這部分就看個人的資料庫欄位設計），因此新增訂單到資料庫的時候， 除了原本的資料還會多加上這幾個 key

但也正是因為多帶了這幾個 key 的資料，打 LinePay 的 API 會得到 2102 JSON 資料格式的錯誤， 因此新增完資料庫的訂單之後，必須記得將非 LinePay 要求的 key 刪除，不然打 API 永遠都會失敗，除了訂單資訊，還要帶上重新導向的 redirectUrls (confirmUrl & cancelUrl)，目的是當訂單狀態變更為成功或失敗時，LinePay 可以呼叫這兩隻 API 來通知後端 (config.callback 為後端 server 的 url)

![](/img/1__53yzFV__H1YnFbXsT__M7vNQ.png)

準備好了 request body 之後，接下來就是準備 request header 了，這裡會需要用加密的方式製做簽章(使用 crypto-js 套件)，將 LinePay 規定的資料組合在一起，加密這邊有個小陷阱，看 LinePay 官方的文件是這樣寫的

![](/img/1__6LBLZ__uCuDyNpCxJTmKGcA.png)

但 crypto-js 的文件是這樣寫的

```javascript
const hmacDigest = Base64.stringify(hmacSHA512(path + hashDigest, privateKey));
```

因此正確的順序應該是加密字串放在前面，secretKey 要放在後面

#### 取得跳轉網址

![](/img/1__OGGFbSWkqTn6tZpI9qOrwg.png)

所有需要的資料都準備好了就可以打 API 了! 如果傳送的資料格式都正確的話，api 的 response 的 returnCode 就會回應 0000，而 info.paymentUrl.web 就是我們要取得的跳轉網址

![](/img/1__gXHkfMXYodf4qgd0fvJcYQ.png)

一開始想要用 res.redirect 的方式由後端直接導轉，但一直顯示 cors error，搞了老半天都無法解決，只好換個方式，後端將 web 跳轉 url 吐給前端，由前端跳轉至 Line Pay 付款頁面 window.location.replace(…)

跳轉至付款頁面

![](/img/1__6b2__j0avGMXiB7Vypgawww.png)

#### 確認訂單狀態

當使用者用 app 掃描畫面上的 qrcode，就會看到以下畫面，不會真的扣款，可以放心按下去

![](/img/1____jNzBNjCuJzodXsqVHUR6Q.jpeg)

成功付款之後，LinePay 就會轉址到後端 server 的 /linpPay/confirm，並且在 url 後面帶上 transactionId 和 orderId，通知後端使用者已經完成付款

![](/img/1__GueztyLHgjxQ__Juva8LJ6g.png)
![](/img/1__EWa__VengiWxPOVQEJyiSgw.png)

不過為了安全性，我們必須再次詢問 LinePay 這筆訂單是否真的付款成功了，這裡的流程可以劃分成三個步驟：

1.  根據剛剛 LinePay 帶在 url 上面的 transactionId，後端再用 POST 的方式呼叫 linePay 的確認訂單 api (${linePay}/payments/${transactionId}/confirm)，一樣必須帶上加密簽章

![](/img/1__elR3YPHj5bi__4d7SZkCH9A.png)

2. 如果訂單成功，LinePay API 的回應結果如下

![](/img/1__5hNi4cbAGjWt88SFk72QPg.png)

3. 後端就可以跳轉到前端付款成功的頁面了(res.direct)

![](/img/1__VUA1Cx0L4Elqp8jQdomP5w.png)

👉 [實作程式碼](https://github.com/ChangChiao/task-board-backend)，可參考[linepay.route.js](https://github.com/ChangChiao/task-board-backend/blob/main/routes/linepay.route.js "linepay.route.js")、linepay.controller.js、linepay.service.js 這三隻檔案，主要邏輯都在 linepay.service.js
