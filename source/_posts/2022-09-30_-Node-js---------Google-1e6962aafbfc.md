---
title: 用Node.js實作第三方登入- Google
description: >-
  當我們要在網頁註冊會員時，相信越來越多人會選擇第三方登入，不用再多跑一次繁瑣的註冊流程，也不需要多記一組帳號密碼，方便許多，因此目前蠻多網站都有提供第三方登入的服務，就是為了要留住這些"過客"，既然這已經成了許多網站的標準配備，剛好近期有在做sideProject，那就來實作看看…
date: "2022-09-30T02:24:28.253Z"
categories: NodeJs
tag: GoogleAuth
keywords: []
---

![](/img/1__Eqn1tUURnxY18OLYK092CQ.jpeg)

當我們要在網頁註冊會員時，相信越來越多人會選擇第三方登入，不用再多跑一次繁瑣的註冊流程，也不需要多記一組帳號密碼，方便許多，因此目前蠻多網站都有提供第三方登入的服務，就是為了要留住這些"過客"，既然這已經成了許多網站的標準配備，剛好近期有在做 sideProject，那就來實作看看吧!

#### 在 GCP 建立新專案

首先進到 [Google Cloud Platform](https://console.cloud.google.com/)頁面，點擊左上角的專案名稱，會開啟彈窗，再點擊彈窗內新增專案的按鈕

![](/img/1__4s023CLM15OzM3Lb3A1t7g.jpeg)

專案名稱可自行命名

![](/img/1__rNTYb10DjeDZGK7uBhHc0w.png)

建立之後，讓子彈飛一會，右上角就會顯示專案已建立完成

![](/img/1__Al6H9z9jPHnNmCDabwm1fA.png)

#### 設定 OAuth 同意畫面

打開側邊選單，選擇 OAuth 同意畫面

![](/img/1__fEM3kVB__qNHnbD6eGBRl3g.png)

沒有打星號的欄位可以跳過不用填

![](/img/1__gZqG3HFreUEBEl0zS7am5g.jpeg)

授權網域請填寫後端 server 的網址，可以設定多個(ex.正式環境、測試環境

![](/img/1__msW56w2nucHeE5pINIPR8Q.jpeg)

如果沒設定授權網域的話等等跳轉就會看到這個錯誤

![](/img/1__R1jXJhJTbQYsfdNldZU2OA.png)

接下來的範圍和測試使用者都可不用填，到最後一步摘要的時候就代表 OAuth 同意畫面的設定已經完成

![](/img/1__mw7U0GhllTTebaeq79y4Sg.png)

#### 設定憑證

點開側邊欄，選擇 API 和服務裡面的憑證

![](/img/1__TrlaI7JfIpJBl3Fi7BUqnA.png)

點擊建立憑證，選擇 OAuth 用戶端 ID

![](/img/1__8W52e6BY106REHvU6uI7kw.png)

應用程式類型請選擇網頁應用程式，名稱隨意，已授權的重新導向 URL 請設定後端 server 的 url，也就是設定白名單的意思

![](/img/1__tDG7ZIM9W87fG34THYrmQw.png)

設定好之後就會拿到用戶端 ID 和密碼囉! 請保管好不要讓其他人取得

![](/img/1__f0wolXyj7LEgHgaXmiN39A.jpeg)

經過一番折騰，總算搞定前置作業了，終於可以開始 coding 啦!

#### 後端實作

_使用 Node.js + Passport 套件_

假設我們直接把金鑰寫在程式碼裡面的話會有暴露的風險，所以我們習慣將這類的金鑰放在.env，.env 記得要加入.gitignore，不然 push 到 repository 還是一樣會被看光光 😅

GOOGLE_CLIENT_ID=xxxxx  
GOOGLE_CLIENT_SECRET=yyyyyy

#### Passport

如果要按部就班的串接 google 登入也是可行，但過程會變得比較繁瑣，因此會推薦使用 Passport 套件，可以幫我們省下不少時間，進到 Passport 官網，搜尋對應的策略，選擇下載量最多的"passport-google-oauth20"

![](/img/1__G0EHxUOjgZDRG7PKLwmPYA.png)

安裝 Passport 套件

npm install --save passport passport-google-oauth20

安裝好 Passport 之後，建立一支設定 Passport 的檔案，檔名隨意，並且在 index.js 引入(如果主程式在 app.js 就是在 app.js 引入)，並且依照官網的說明，使用 GoogleStrategy 並且帶入 clientId 、 clientSecret 以及 google 授權成功後要打的網址，這部分稍後會有詳細的說明，(config.callback 為後端 server 的 url)

#### 登入 API

新增 google 登入的路由，也就是當前端按下登入按鈕後要打的 api，這邊的 scope 代表要請求授權的資料範圍，因為資料庫需要用到使用者的信箱和大頭貼，所以需要 email 和 profile 這兩個欄位

#### 前端實作

![](/img/1__Wt__ViNg5lkkogreg8dWzsg.png)

點擊登入按扭後，會跳轉到後端剛剛設定的的${backend}/auth/google，passport 會幫我們自動導向到 [https://accounts.google.com/o/oauth2/auth](https://accounts.google.com/o/oauth2/auth)的 google 授權頁面

![](/img/1__BSeNXeRxQUw8C8C3u7YuGw.jpeg)

#### 後端 callback API

![](/img/1__FUefjS7S2cgazW4Fc9vd4Q.png)

當使用者確定授權之後，google 就會轉址到我們設定的這支 callback api 並且會在 query 帶上 code

![](/img/1__8VLTzHY4ZswN7YGhBraf5g.png)

上述的程式碼看起來很簡單，但其實中間 passport 幫我們處理掉蠻多事情的，詳細的步驟如下

1.  Passport 會拿著剛剛在 query 取得的 code，去跟 google 拿 token \[POST\](https://oauth2.googleapis.com/token)
2.  取得 token 之後再去跟 google 請求完整的使用者資料\[GET\](https://www.googleapis.com/oauth2/v1/userinfo)
3.  拿到資料後 Passport 就會執行我們自己定義的 signInByGoogle callback function

我們可以在 req 參數裡面取得 google 回傳的資料(ex: email、 name、 picture)等等，假設是新用戶的話就新增資料到 DB，並且建立一組 token 給使用者，至於 token 要怎麼在轉址的時候傳給前端，目前想到的兩個方法是帶在轉址的 url 上，或是用後端用 res.cookie(‘token’, xxxx)的方式，把 token 存在瀏覽器的 cookie，如果有其他的做法，歡迎分享給我!

最後一步，後端用 res.redirect(前端網址)的方式跳轉到登入成功的畫面，就大功告成了!

👉 [實作程式碼](https://github.com/ChangChiao/task-board-backend)

- passport 相關設定放在 config/passport.js
- route 相關設定放在 routes/auth.route.js
- 第三方登入邏輯放在 oauth.service.js
