---
title: 用Node.js實作第三方登入- Google
description: >-
  當我們要在網頁註冊會員時，相信越來越多人會選擇第三方登入，不用再多跑一次繁瑣的註冊流程，也不需要多記一組帳號密碼，方便許多，因此目前蠻多網站都有提供第三方登入的服務，就是為了要留住這些"過客"，既然這已經成了許多網站的標準配備，剛好近期有在做sideProject，那就來實作看看…
date: '2022-09-30T02:24:28.253Z'
categories: []
keywords: []
slug: >-
  /@joe-chang/%E7%94%A8node-js%E5%AF%A6%E4%BD%9C%E7%AC%AC%E4%B8%89%E6%96%B9%E7%99%BB%E5%85%A5-google-1e6962aafbfc
---

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__Eqn1tUURnxY18OLYK092CQ.jpeg)

當我們要在網頁註冊會員時，相信越來越多人會選擇第三方登入，不用再多跑一次繁瑣的註冊流程，也不需要多記一組帳號密碼，方便許多，因此目前蠻多網站都有提供第三方登入的服務，就是為了要留住這些"過客"，既然這已經成了許多網站的標準配備，剛好近期有在做sideProject，那就來實作看看吧!

#### 在GCP建立新專案

首先進到 [Google Cloud Platform](https://console.cloud.google.com/)頁面，點擊左上角的專案名稱，會開啟彈窗，再點擊彈窗內新增專案的按鈕

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__4s023CLM15OzM3Lb3A1t7g.jpeg)

專案名稱可自行命名

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__rNTYb10DjeDZGK7uBhHc0w.png)

建立之後，讓子彈飛一會，右上角就會顯示專案已建立完成

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__Al6H9z9jPHnNmCDabwm1fA.png)

#### 設定OAuth同意畫面

打開側邊選單，選擇OAuth同意畫面

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__fEM3kVB__qNHnbD6eGBRl3g.png)

沒有打星號的欄位可以跳過不用填

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__gZqG3HFreUEBEl0zS7am5g.jpeg)

授權網域請填寫後端server的網址，可以設定多個(ex.正式環境、測試環境

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__msW56w2nucHeE5pINIPR8Q.jpeg)

如果沒設定授權網域的話等等跳轉就會看到這個錯誤

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__R1jXJhJTbQYsfdNldZU2OA.png)

接下來的範圍和測試使用者都可不用填，到最後一步摘要的時候就代表OAuth同意畫面的設定已經完成

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__mw7U0GhllTTebaeq79y4Sg.png)

#### 設定憑證

點開側邊欄，選擇API和服務裡面的憑證

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__TrlaI7JfIpJBl3Fi7BUqnA.png)

點擊建立憑證，選擇OAuth用戶端ID

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__8W52e6BY106REHvU6uI7kw.png)

應用程式類型請選擇網頁應用程式，名稱隨意，已授權的重新導向URL請設定後端server的url，也就是設定白名單的意思

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__tDG7ZIM9W87fG34THYrmQw.png)

設定好之後就會拿到用戶端ID和密碼囉! 請保管好不要讓其他人取得

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__f0wolXyj7LEgHgaXmiN39A.jpeg)

經過一番折騰，總算搞定前置作業了，終於可以開始coding啦!

#### 後端實作

_使用Node.js + Passport 套件_

假設我們直接把金鑰寫在程式碼裡面的話會有暴露的風險，所以我們習慣將這類的金鑰放在.env，.env記得要加入.gitignore，不然push到repository還是一樣會被看光光😅

GOOGLE\_CLIENT\_ID=xxxxx  
GOOGLE\_CLIENT\_SECRET=yyyyyy

#### Passport

如果要按部就班的串接google登入也是可行，但過程會變得比較繁瑣，因此會推薦使用Passport套件，可以幫我們省下不少時間，進到Passport官網，搜尋對應的策略，選擇下載量最多的"passport-google-oauth20"

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__G0EHxUOjgZDRG7PKLwmPYA.png)

安裝Passport套件

npm install --save passport passport-google-oauth20

安裝好Passport之後，建立一支設定Passport的檔案，檔名隨意，並且在index.js引入(如果主程式在app.js就是在app.js引入)，並且依照官網的說明，使用GoogleStrategy並且帶入clientId 、 clientSecret 以及google授權成功後要打的網址，這部分稍後會有詳細的說明，(config.callback為後端server的url)

#### 登入API

新增google登入的路由，也就是當前端按下登入按鈕後要打的api，這邊的scope代表要請求授權的資料範圍，因為資料庫需要用到使用者的信箱和大頭貼，所以需要email和profile這兩個欄位

#### 前端實作

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__Wt__ViNg5lkkogreg8dWzsg.png)

點擊登入按扭後，會跳轉到後端剛剛設定的的${backend}/auth/google，passport會幫我們自動導向到 [https://accounts.google.com/o/oauth2/auth](https://accounts.google.com/o/oauth2/auth)的google授權頁面

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__BSeNXeRxQUw8C8C3u7YuGw.jpeg)

#### 後端callback API

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__FUefjS7S2cgazW4Fc9vd4Q.png)

當使用者確定授權之後，google就會轉址到我們設定的這支callback api並且會在query帶上code

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__8VLTzHY4ZswN7YGhBraf5g.png)

上述的程式碼看起來很簡單，但其實中間passport幫我們處理掉蠻多事情的，詳細的步驟如下

1.  Passport會拿著剛剛在query取得的code，去跟google拿token \[POST\](https://oauth2.googleapis.com/token)
2.  取得token之後再去跟google請求完整的使用者資料\[GET\](https://www.googleapis.com/oauth2/v1/userinfo)
3.  拿到資料後Passport就會執行我們自己定義的signInByGoogle callback function

我們可以在req參數裡面取得google回傳的資料(ex: email、 name、 picture)等等，假設是新用戶的話就新增資料到DB，並且建立一組token給使用者，至於token要怎麼在轉址的時候傳給前端，目前想到的兩個方法是帶在轉址的url上，或是用後端用res.cookie(‘token’, xxxx)的方式，把token存在瀏覽器的cookie，如果有其他的做法，歡迎分享給我!

最後一步，後端用res.redirect(前端網址)的方式跳轉到登入成功的畫面，就大功告成了!

👉 [實作程式碼](https://github.com/ChangChiao/task-board-backend)

*   passport相關設定放在config/passport.js
*   route相關設定放在 routes/auth.route.js
*   第三方登入邏輯放在 oauth.service.js