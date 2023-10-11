---
title: 網頁如何及時通訊? Polling v.s WebSocket
description: >-
  有天我用line pay在某某購物網買東西， 突然想到為甚麼每次我用手機付款成功後，網頁就能夠及時告訴我付款成功呢?
  於是便打開chrome開發者工具觀察了一下， 發現網頁會定期(每秒)打API去要資料， 就能及時更新我的付款狀態。
date: "2021-06-12T08:54:15.994Z"
categories: web
tag: web
keywords: []
---

![](/img/1__NJ8PcBtOMlTnVNtoEq__Hog.jpeg)

有天我用 line pay 在某某購物網買東西， 突然想到為甚麼每次我用手機付款成功後，網頁就能夠及時告訴我付款成功呢? 於是便打開 chrome 開發者工具觀察了一下， 發現網頁會定期(每秒)打 API 去要資料， 就能及時更新我的付款狀態。

#### Polling

以上述例子而言， 就叫做**Polling**，簡單來說就是每隔一段時間就發送請求給 Server 端 (利用 JS 的 setTimeout or setInterval 來達成)，以確保 Client 端可以拿到新的資料 ，早期都是用這種方式來即時更新資料，不過缺點是頻繁的請求 ，而每次的請求又未必有資料更新， 就會浪費 Client 端的網路資源了。

![](/img/1__NdxRMQfNFZJR1xYFQEtpZA.png)

#### Long-Polling

與 Polling 一樣的是 Client 端會發送一個 request 給 Server 端，只不過 Server 端會等待一段時間才會回 response，當 Client 端拿到 response 之後再次發出請求，相較於一般的 Polling 比較有效率，等 Server 端有資料或者等待的時間已經到了(Timeout)再回應，能夠有效減少 Client 端不必要的請求次數。

![](/img/1__w9XfqbdnHylkSTJatwjJoA.png)

#### WebSocket

> WebSocket 是一種讓瀏覽器與伺服器進行一段互動通訊的技術。這個 API 在不必輪詢（poll）伺服器下，讓使用者傳送訊息至伺服器並接受事件驅動回應。(引用自 MDN)

以往都是 Client 端主動跟 Server 端請求資料， 但 WebSocket 能夠建立雙向的溝通 ，建立連接後 Client 端和 Server 端可以互相傳遞資料，因此 Server 端有新資料的時候也能夠主動的通知 Client 端了，不再處於被動的角色，因此適合拿來作聊天室或是多人連線遊戲。

![](/img/1__jn1DPRHAiM9cMSPHXn0S5A.png)

缺點的話， 部分老舊瀏覽器不支援(不過那些骨董級版本應該用的人少之又少吧)，以及伺服器的成本較高。

![](/img/1__w7CtVojiHYRhWkuCto0pQA.png)

前端實作的程式碼如下:

![](/img/1__OFplo5PUqb1DdSnlSWKWHQ.png)

與傳統 polling 相比 ，WebSocket 用的網路流量差異非常懸殊

![](/img/1__0U6gWzxvrkRTFdcK0b76CQ.gif)

目前工作上大部分的專案還是採用 polling 的方式， 希望之後如果有專案是需要即時更新資料的話，能夠都使用 WebSocket 來開發，除了節省資源以外，也能夠做到真正的即時響應，不過也要後端能夠配合使用就是了。

參考資源:

[Polling, server sent events, and WebSockets: system design interview concepts (8 of 9)](https://igotanoffer.com/blogs/tech/polling-sse-websockets-system-design-interview)

[獲得實時更新的方法（Polling, Comet, Long Polling, WebSocket）](https://blog.niclin.tw/2017/10/28/%E7%8D%B2%E5%BE%97%E5%AF%A6%E6%99%82%E6%9B%B4%E6%96%B0%E7%9A%84%E6%96%B9%E6%B3%95polling-comet-long-polling-websocket/)
