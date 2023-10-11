---
title: 了解window Location 物件
description: 假設今天我們需要獲取網址列的資訊，可以怎麼做？想必大家location.href並不陌生吧？
date: "2020-02-24T13:24:08.664Z"
tags: javascript
categories: web
keywords: []
---

![](/img/1__cOvGChemcFOVFZmRk5__KmA.jpeg)

假設今天我們需要獲取網址列的資訊，可以怎麼做？想必大家對 location.href 並不陌生吧？

假設今天我們有一個網址

#### [https://vuejs.org:8080/support-vuejs?tid=123456#One-time-Donations](https://vuejs.org:8080/support-vuejs?tid=123456#One-time-Donations)

可以有這些方法取的其他的 URL 資訊

```javascript

// 獲取域名
location.host 👉 "vuejs.org"

// 獲取域名+port （一般的網址預設為:80 port 不會顯示在網址上
location.hostname 👉 "vuejs.org:8080"

// 獲取完整 URL
location.href 👉
"https://vuejs.org:8080/support-vuejs?tid=123456#One-time-Donations"

// 獲取 web 協議 + 域名
location.origin 👉
"https://vuejs.org"

//獲取 URL 路徑
location.pathname 👉
"/support-vuejs/"

//獲取 port 號
location.port 👉
"8080"

//獲取 hash 值
location.hash 👉
"#One-time-Donations"

//獲取 URL scheme（http: 或 https:）
location.protocol 👉
"https:"

//獲取 URL 参数
locaiton.search 👉
"?tid=222"

```

這邊順便提到 javascript 頁面跳轉、重新整理、導頁的方法

```javascript

// 取代當前頁面
location.replace

P.S 瀏覽紀錄不會被保存在 history 裡，因此無法按上一頁

// 跳轉頁面
locaiton.assign(url)
locaiton.href = "url"

P.S 瀏覽紀錄均記錄在 history 裡，可按上一頁


// 返回上一頁
history back
部份版本瀏覽器不支援

history go -1

// 返回下一頁
history.forward()

// 重新整理頁面
history.go(0)

location.reload()
其實這個方法可以傳參數 預設為 false
重整時就會從 cache 讀取資源

location.reload(true)
傳入 true 就會強制瀏覽器又去 server 那邊請求資源
等同於 Ctrl +F5

補充一個比較少用的

嵌入的 iframe 獲取父層的網址

parent.window.location

```

#### 關於瀏覽器的歷史紀錄

window.history 物件會紀錄使用者瀏覽網頁的紀錄，但基於隱私，我們無法知道詳細的 URL 是什麼，唯一能獲得的資訊就是 history length ，瀏覽器的瀏覽紀錄筆數，開新頁面的時候 length 為 1。

#### 實作 SPA

如果曾經用過 vue cli 的人，一定對 spa 不陌生，頁面透過 hash 的變化，不重整頁面就可以切換不同的內容，HTML5 裡引用了新的 API，history.pushState 和 history.replaceState，可以拿來實作 SPA (Single Page Application)，改變 url 的 hash 而不會 relaod 頁面。

```javascript
let state = {
  pageInfo: "production",
};
//第一個參數是儲存的物件
//第二個是新頁面的標題
//第三個是要跳轉的網址

history.pushState(state, "product", "/product");

//不會刷新頁面 網址跳轉會記錄在瀏覽器紀錄中 ，history length +1。
history.pushState(state, title, url);

//不會刷新頁面，直接修改當前瀏覽紀錄 history length 維持不變。
history.replaceState();
```

#### 監聽 hash 的變化

```javascript
window.addEventListener(
  "hashchange",
  (event) => {
    console.log(event);
  },
  false
);
```

#### popstate

當使用者點擊上一頁或下一頁就會觸發 popstate 事件

```javascript
//獲得先前的 state 狀態
window.addEventListener("popstate", function (event) {
  var state = event.state;
  //do something(state.pageInfo);
});
```

history.back()、history.forward()、history.go()是會觸發 popstate 事件，
但是 history.pushState()和 history.replaceState()不會觸發 popstate 事件。
