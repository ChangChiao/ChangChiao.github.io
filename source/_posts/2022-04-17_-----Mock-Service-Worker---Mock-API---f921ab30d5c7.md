---
title: 別等了！用Mock Service Worker來產生Mock API吧！
description: >-
  一般的前後端分離開發流程通常是由後端開發好API之後交給前端來做串接，不過很常發生的情況是前端早就完成了前置作業，只能望穿秋水等待後端API開發完成，這個時候就可以使用Mock
  API來模擬真實的API，讓前端可以自給自足，先行一步串接API，除此之外，使用Mock…
date: '2022-04-17T04:39:55.703Z'
categories: []
keywords: []
slug: >-
  /@joe-chang/%E5%88%A5%E7%AD%89%E4%BA%86-%E7%94%A8mock-service-worker%E4%BE%86%E7%94%A2%E7%94%9Fmock-api%E5%90%A7-f921ab30d5c7
---

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__kalnHosFvOQaoWouz3ZYcA.jpeg)

一般的前後端分離開發流程通常是由後端開發好API之後交給前端來做串接，不過很常發生的情況是前端早就完成了前置作業，只能望穿秋水等待後端API開發完成，這個時候就可以使用Mock API來模擬真實的API，讓前端可以自給自足，先行一步串接API，除此之外，使用Mock API還有一項優點，可以模擬如果API壞掉了，前端的程式是否會跟著爆掉，進而做一些意外的錯誤處理，畢竟在開發環境API串接得好好都沒事，但在正式環境因為API錯誤而導致前端頁面跟著壞掉的情況並不少見，因此可以用Mock API來模擬那些可能發生的例外情形來做測試。

> **什麼是Mock API？ —** 在測試的環境當中，模擬真實環境的API

Mock API的選擇其實不少，先前都是使用json-server來開發，近期無意間發現有個新崛起的明日之星 — Mock Service Worker，支援RESTful 和 GraphQL，npm近期的下載量已經超越json-server了！那麼今天就試著來玩玩看Mock Service Worker吧！

#### 關於Service Worker

Mock Service Worker在瀏覽器環境的實作原理是使用service worker這個WEB API來攔截http request，再將API response的內容替換成我們模擬的資料，流程可參考下圖

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__R8vnAecuP5FhhoSahbCJsw.jpeg)

因此讓我們先來認識這個神奇的service worker吧！

service worker可以理解是client(瀏覽器)和server端之間的一個Proxy(代理伺服器)，這個Proxy可以做的事情有：攔截client端的API請求、主動的發起API請求給server，推送消息給client等等， 而service worker最大的優勢是可以長時間的cache資料，因此最常被使用在PWA上面，讓網頁也能像APP那樣離線使用

**Service Worker 特點**

*   因為安全考量，只能在https和localhost(本機測試環境)使用service worker
*   會在瀏覽器背景執行，不影響瀏覽器的主執行緒
*   Service Worker不能操作DOM

如果有啟動Service Worker的話，可以在開發者工具中的application/Service Worker看到相關的資訊

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__HXaQYfizT3BmWl4hhyN__kg.png)

因為文章篇幅有限，關於Service Worker的生命週期和事件監聽就不多做介紹，有興趣的讀者可以閱讀這篇[文章](https://ithelp.ithome.com.tw/articles/10216819)

#### Mock Service Worker 初體驗

這次測試的專案我是使用vite + React，大家可以選擇自己熟悉的打包工具和框架來測試，我要衷心的讚嘆一下官方文件寫的清楚明暸，非常好上手，以下都會以msw來簡稱Mock Service Worker

第一步先安裝msw

在src資料夾底下建立一個mocks的資料夾，之後會將相關的程式碼都放在這個資料夾底下

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__uKv7PmAE8k3ambVX8iUXFQ.png)

#### 建立handler

接下來要定義Mock API內容，新增一隻handler.js，並且引入rest，我們可以在handlers陣列裡面定義多個rest handler

rest擁有RESTful API對應的METHOD方法，像是rest.post()、rest.get()、rest.delete()等等，用法也很簡單傳入的第一個參數是API的url，第二個參數則是一個callback function，而這個callback function有三個參數可以使用，介紹如下

*   req: 接收API request資訊
*   res: 回應API的請求
*   ctx : 可以設定status code、header、body內容等等

**題外話**

> 如果有寫過node.js的朋友對於msw的用法應該會覺得十分親切，語法雖然有點差異，但概念還蠻相似的，都是透過req拿到請求的資訊，並且用res回應API請求

接下來直接複製官網的範例來使用，下面的handler模擬了登入和獲取使用者資訊的流程，如果在未登入的情形下請求 user API，API會回應403(使用者沒被授權），如果有登入的話status code則會回應200

#### 建立Service Worker

執行以下指令讓msw幫你在public底下建立Service Worker，請將PUBLIC\_DIR替換成你專案底下的public的資料夾路徑

可是… 我的專案底下沒有public資料夾啊，沒關係官網列出了常見的專案public路徑，是不是很貼心？

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__fFw5lXRJ__1LFG3bBY1LedQ.png)

執行完畢之後，可以看public資料夾底下生成了mockServiceWorker.js，裡面撰寫了和Service Worker有關的程式碼

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__VbT8rShd__JAXiwsfmR5nhg.png)

下一步，就是如何啟動這個Service Worker了！先在src/mocks資料夾底下新增browser.js

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__BJ48i8JxFxGLhTDlHECJVw.png)

引入setupWorker來創建worker實例，並且將先前定義好的handler做為參數傳入

畢竟只是要在開發環境測試Mock API，因此寫了一個簡單的判斷，當開發環境是dev才讓worker run起來，因為我是用vite開發，所以判斷是否為dev環境是用import.meta.env.DEV，用webpack 的朋友請用 process.env.NODE\_ENV判斷

msw的前置作業已經大功告成，接下來將專案run起來，如果有在console.log看到 \[MSW\] Mocking enabled.，就代表msw已經成功啟動了

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__YjSzyfD7WpycOeavFH3Piw.png)

為了模擬登入請求，寫了一個簡單的登入按鈕，點擊之後會呼叫登入API，如果登入成功，就會接著請求user的資料

當我們點擊了登入按鈕之後打開f12觀察，會發現api的status code後面多了(from service worker)的字眼，提醒你這隻API是由Service worker攔截請求的

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__3vwd5dLRUgDdzpR__oY0__lg.png)

查看fetch的結果也的確有拿到我們在handler定義的測試內容，登入成功就會回傳login success的訊息

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__aOoBsp9ATWVkT0QIApDIaw.png)

下一步，我想要改寫一下官網的範例，模擬登入後可以查看自己的購物車清單，假設有二十筆的商品資料好了，要如何快速生成20筆假資料？ 土法煉鋼自己一筆一筆key也是個方法，但實在是太累了，我們可以讓faker.js來代勞

#### faker.js — 假資料製造機

先安裝faker.js

faker提供了很多假資料模組，像是個資、商品、公司行號等等，而且官網還強調資料不會太假XD，因為要模擬購物車的商品資料，所以選擇使用Commerce模組，使用faker.commerce.productName()的語法就可以生成一個商品名稱，price可以傳入參數，來限制生成的假資料價錢範圍，小數點後幾位等等，以下面的例子來說，限定price在100~1000之間，並且小數點後為0，更多的屬性可以參考[這裡](https://fakerjs.dev/api/commerce.html)

在handler.js引入faker.js，並且跑迴圈生成假資料列表

呼叫user API時，就可以成功看到購物車的20筆假資料！

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__24qQJ1bXSP70NuQZyTUKEg.png)

以上就是關於Mock Service Worker的簡單介紹，有錯歡迎指正！謝謝

[完整程式碼](https://github.com/ChangChiao/msw_test)

參考資料：[Mock Service Worker](https://mswjs.io/)、[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Service_Worker_API/Using_Service_Workers)