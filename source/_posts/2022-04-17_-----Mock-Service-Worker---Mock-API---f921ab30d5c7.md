---
title: 別等了！用Mock Service Worker來產生Mock API吧！
description: >-
  一般的前後端分離開發流程通常是由後端開發好API之後交給前端來做串接，不過很常發生的情況是前端早就完成了前置作業，只能望穿秋水等待後端API開發完成，這個時候就可以使用Mock
  API來模擬真實的API，讓前端可以自給自足，先行一步串接API，除此之外，使用Mock…
date: "2022-04-17T04:39:55.703Z"
categories: []
keywords: []
slug: >-
  /@joe-chang/%E5%88%A5%E7%AD%89%E4%BA%86-%E7%94%A8mock-service-worker%E4%BE%86%E7%94%A2%E7%94%9Fmock-api%E5%90%A7-f921ab30d5c7
---

![](/img/1__kalnHosFvOQaoWouz3ZYcA.jpeg)

一般的前後端分離開發流程通常是由後端開發好 API 之後交給前端來做串接，不過很常發生的情況是前端早就完成了前置作業，只能望穿秋水等待後端 API 開發完成，這個時候就可以使用 Mock API 來模擬真實的 API，讓前端可以自給自足，先行一步串接 API，除此之外，使用 Mock API 還有一項優點，可以模擬如果 API 壞掉了，前端的程式是否會跟著爆掉，進而做一些意外的錯誤處理，畢竟在開發環境 API 串接得好好都沒事，但在正式環境因為 API 錯誤而導致前端頁面跟著壞掉的情況並不少見，因此可以用 Mock API 來模擬那些可能發生的例外情形來做測試。

> **什麼是 Mock API？ —**  在測試的環境當中，模擬真實環境的 API

Mock API 的選擇其實不少，先前都是使用 json-server 來開發，近期無意間發現有個新崛起的明日之星  — Mock Service Worker，支援 RESTful 和 GraphQL，npm 近期的下載量已經超越 json-server 了！那麼今天就試著來玩玩看 Mock Service Worker 吧！

#### 關於 Service Worker

Mock Service Worker 在瀏覽器環境的實作原理是使用 service worker 這個 WEB API 來攔截 http request，再將 API response 的內容替換成我們模擬的資料，流程可參考下圖

![](/img/1__R8vnAecuP5FhhoSahbCJsw.jpeg)

因此讓我們先來認識這個神奇的 service worker 吧！

service worker 可以理解是 client(瀏覽器)和 server 端之間的一個 Proxy(代理伺服器)，這個 Proxy 可以做的事情有：攔截 client 端的 API 請求、主動的發起 API 請求給 server，推送消息給 client 等等， 而 service worker 最大的優勢是可以長時間的 cache 資料，因此最常被使用在 PWA 上面，讓網頁也能像 APP 那樣離線使用

**Service Worker 特點**

- 因為安全考量，只能在 https 和 localhost(本機測試環境)使用 service worker
- 會在瀏覽器背景執行，不影響瀏覽器的主執行緒
- Service Worker 不能操作 DOM

如果有啟動 Service Worker 的話，可以在開發者工具中的 application/Service Worker 看到相關的資訊

![](/img/1__HXaQYfizT3BmWl4hhyN__kg.png)

因為文章篇幅有限，關於 Service Worker 的生命週期和事件監聽就不多做介紹，有興趣的讀者可以閱讀這篇[文章](https://ithelp.ithome.com.tw/articles/10216819)

#### Mock Service Worker  初體驗

這次測試的專案我是使用 vite + React，大家可以選擇自己熟悉的打包工具和框架來測試，我要衷心的讚嘆一下官方文件寫的清楚明暸，非常好上手，以下都會以 msw 來簡稱 Mock Service Worker

第一步先安裝 msw

在 src 資料夾底下建立一個 mocks 的資料夾，之後會將相關的程式碼都放在這個資料夾底下

![](/img/1__uKv7PmAE8k3ambVX8iUXFQ.png)

#### 建立 handler

接下來要定義 Mock API 內容，新增一隻 handler.js，並且引入 rest，我們可以在 handlers 陣列裡面定義多個 rest handler

rest 擁有 RESTful API 對應的 METHOD 方法，像是 rest.post()、rest.get()、rest.delete()等等，用法也很簡單傳入的第一個參數是 API 的 url，第二個參數則是一個 callback function，而這個 callback function 有三個參數可以使用，介紹如下

- req: 接收 API request 資訊
- res: 回應 API 的請求
- ctx : 可以設定 status code、header、body 內容等等

**題外話**

> 如果有寫過 node.js 的朋友對於 msw 的用法應該會覺得十分親切，語法雖然有點差異，但概念還蠻相似的，都是透過 req 拿到請求的資訊，並且用 res 回應 API 請求

接下來直接複製官網的範例來使用，下面的 handler 模擬了登入和獲取使用者資訊的流程，如果在未登入的情形下請求 user API，API 會回應 403(使用者沒被授權），如果有登入的話 status code 則會回應 200

#### 建立 Service Worker

執行以下指令讓 msw 幫你在 public 底下建立 Service Worker，請將 PUBLIC_DIR 替換成你專案底下的 public 的資料夾路徑

可是… 我的專案底下沒有 public 資料夾啊，沒關係官網列出了常見的專案 public 路徑，是不是很貼心？

![](/img/1__fFw5lXRJ__1LFG3bBY1LedQ.png)

執行完畢之後，可以看 public 資料夾底下生成了 mockServiceWorker.js，裡面撰寫了和 Service Worker 有關的程式碼

![](/img/1__VbT8rShd__JAXiwsfmR5nhg.png)

下一步，就是如何啟動這個 Service Worker 了！先在 src/mocks 資料夾底下新增 browser.js

![](/img/1__BJ48i8JxFxGLhTDlHECJVw.png)

引入 setupWorker 來創建 worker 實例，並且將先前定義好的 handler 做為參數傳入

畢竟只是要在開發環境測試 Mock API，因此寫了一個簡單的判斷，當開發環境是 dev 才讓 worker run 起來，因為我是用 vite 開發，所以判斷是否為 dev 環境是用 import.meta.env.DEV，用 webpack 的朋友請用 process.env.NODE_ENV 判斷

msw 的前置作業已經大功告成，接下來將專案 run 起來，如果有在 console.log 看到 \[MSW\] Mocking enabled.，就代表 msw 已經成功啟動了

![](/img/1__YjSzyfD7WpycOeavFH3Piw.png)

為了模擬登入請求，寫了一個簡單的登入按鈕，點擊之後會呼叫登入 API，如果登入成功，就會接著請求 user 的資料

當我們點擊了登入按鈕之後打開 f12 觀察，會發現 api 的 status code 後面多了(from service worker)的字眼，提醒你這隻 API 是由 Service worker 攔截請求的

![](/img/1__3vwd5dLRUgDdzpR__oY0__lg.png)

查看 fetch 的結果也的確有拿到我們在 handler 定義的測試內容，登入成功就會回傳 login success 的訊息

![](/img/1__aOoBsp9ATWVkT0QIApDIaw.png)

下一步，我想要改寫一下官網的範例，模擬登入後可以查看自己的購物車清單，假設有二十筆的商品資料好了，要如何快速生成 20 筆假資料？ 土法煉鋼自己一筆一筆 key 也是個方法，但實在是太累了，我們可以讓 faker.js 來代勞

#### faker.js —  假資料製造機

先安裝 faker.js

faker 提供了很多假資料模組，像是個資、商品、公司行號等等，而且官網還強調資料不會太假 XD，因為要模擬購物車的商品資料，所以選擇使用 Commerce 模組，使用 faker.commerce.productName()的語法就可以生成一個商品名稱，price 可以傳入參數，來限制生成的假資料價錢範圍，小數點後幾位等等，以下面的例子來說，限定 price 在 100~1000 之間，並且小數點後為 0，更多的屬性可以參考[這裡](https://fakerjs.dev/api/commerce.html)

在 handler.js 引入 faker.js，並且跑迴圈生成假資料列表

呼叫 user API 時，就可以成功看到購物車的 20 筆假資料！

![](/img/1__24qQJ1bXSP70NuQZyTUKEg.png)

以上就是關於 Mock Service Worker 的簡單介紹，有錯歡迎指正！謝謝

[完整程式碼](https://github.com/ChangChiao/msw_test)

參考資料：[Mock Service Worker](https://mswjs.io/)、[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Service_Worker_API/Using_Service_Workers)
