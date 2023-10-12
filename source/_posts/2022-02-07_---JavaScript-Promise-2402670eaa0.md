---
title: 優雅的JavaScript Promise
description: >-
  以往在處理非同步流程時，如果要確保執行順序並且帶入先前執行的結果會採用callback的方式，但很容易就會變成callback hell
  回調地獄(如下圖)，不易閱讀之外也很難維護，而Promise的出現，可以優雅的解決這個問題
date: '2022-02-07T10:17:57.107Z'
categories: []
keywords: []
slug: /@joe-chang/%E5%84%AA%E9%9B%85%E7%9A%84javascript-promise-2402670eaa0
---

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__YpWbqiMz5wNQIfM09GRnAA.jpeg)

以往在處理非同步流程時，如果要確保執行順序並且帶入先前執行的結果會採用callback的方式，但很容易就會變成callback hell 回調地獄(如下圖)，不易閱讀之外也很難維護，而Promise的出現，可以優雅的解決這個問題

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__9yiIpaZcU4Dc1Tip6KBvbg.png)

一個簡單Promise結構如下，先用new建立一個Promise物件，就可以使用then()來接續之後要做的事情，有點像jQuery那樣的鏈式寫法，一個接一個，因此就可以將原本複雜的巢狀結構變成扁平化，Promise會傳入兩個函式resolve和reject，執行resolve會將Promise的狀態改為執行成功(fullfilled)，執行reject的話Promise的狀態則會變成執行失敗(rejected)

#### Promise的三個狀態

Promise擁有三個狀態 : 等待中（Pending）、執行成功(Fulfilled)、執行失敗(Rejected)，一但狀態從等待中變成成功或是失敗就不能再異動

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__eWEqIUtLKDIxEyjhY__F1Sg.png)

*   pending (等待中) — Promise的初始狀態
*   fullfilled (已完成) — 執行resolve()，Promise狀態會變成fullfilled
*   rejected(已拒絕) — 執行reject()，Promise狀態會變成rejected

#### Promise.then()

then()可以傳入兩個參數，第一個是執行成功(onFulfilled)的函式，第二個是執行失敗(onRejected)的函式，執行成功的函式會在promise物件的狀態變成resolved的時候執行，執行失敗的函式則是在Promise物件的狀態變成rejected的時候執行

一個Promise執行完畢之後會return一個Promise，並將執行結果拋給then()來接收，就可以做後續的處理，如果then()不是return Promise而是return 一般的值，then就會把這個值當作是成功的結果拋給下一個then

以下圖的例子來說，Promise.then接收到resolve的結果，透過then()逐一做加工(加上年齡、性別等等)，最後可以得到一個完整的物件

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__wV8ug80CFUXrtVL5nUWjnw.png)

#### Promise.catch()

假設在.then的階段發生異常該怎麼處理呢?就像try catch那樣，可以用catch來捕捉錯誤，以下面的例子來說，刻意在then的執行成功函式裡拋出一個錯誤，就會進到catch裡面

#### Promise.finally()

無論promise物件的狀態是成功失敗，或是有意外的錯誤都會執行finally裡面的函式

#### Promise.resolve和Promise.reject

> Promise.resolve(value) 方法回傳一個以 value 判定結果的 `[Promise](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise)` 物件。若 value 是個 thenable (例如，具有 `["then"方法](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise/then)`)，則回傳的 promise 將依其結果採取其最終狀態；若 value 是 promise，則作為呼叫 Promise.resolve 之結果；其他情形都將回傳以 value 實現的 promise。(引用自MDN)

簡單來說使用Promise.resolve的語法可以直接產生一個Fulfilled的Promise物件，只會進入執行成功(onFulfilled)的函式，反過來說Promise.reject就只會執行失敗(onRejected)的函式

#### Promise.all()

假設我們需要等待多個非同步執行完的結果，才能做後續的處理，就可以使用 Promise.all()，Promise.all()接受一個陣列參數，陣列裡可以帶入多個Promise物件，當全部的Promise都resolve的時候就會執行then方法，如果其中有一個Promise reject或是發生錯誤的話就會執行onRejected函式，可以想成是跟團旅行，有的人會提早到，有人則是會遲到，導遊必須等到所有人都到齊了才會出發(then)，萬一有人因故未到(reject)，這個行程就會無法成行(onRejected)，假設前端的畫面呈現是需要依賴多隻api的回傳結果，就很適合用Promise.all()來處理

#### Promise.race()

和Promise.all()不同之處在於 Promise.race()只要有一個Promise執行resolve或是reject，就會進入到then()，並且只會回傳第一個Promise物件的結果，就好比說有一場馬拉松比賽，只有冠軍一個獎項，因此只要有一個選手抵達終點比賽就結束了，不過說真的在工作實務上還沒有用過Promise.race()就是了，目前看到應用的例子是假設後端api三秒過後都還沒有回應，就跳出api回應逾時的提示

#### catch v.s onRejected

在查詢與Promise相關的資料時，發現當Promise執行reject()時，有人會用catch來捕獲錯誤，有人則是用onRejected的函式，那假如onRejected函式和catch同時並存會發生甚麼事呢? 稍微測試了一下，發現當Promise執行reject()僅會執行onRejected函式並不會進到catch，真的是越看越困惑😂

後來終於了解兩者的差異在哪裡

*   **catch**: 所有promise的錯誤或是reject都能處理
*   **onRejected**: 僅能處理reject

如果今天只是單純要處理reject的後續行為，用onRejected或是catch都能達成，但如果是要捕獲在onFulfilled或是onRejected發生的錯誤，就只有catch能辦到，可以想成catch是比較萬能的!

經過這一系列的練習實作，總算對Promise的認識又上一層樓了，也順便釐清了某些一知半解的問題，像是onRejected和catch的差異，如果沒有實際寫一次還真的會以為兩者差不多😂，以上就是關於Promise的簡單介紹，如果發現有錯誤歡迎指正