---
title: 優雅的JavaScript Promise
description: >-
  以往在處理非同步流程時，如果要確保執行順序並且帶入先前執行的結果會採用callback的方式，但很容易就會變成callback hell
  回調地獄(如下圖)，不易閱讀之外也很難維護，而Promise的出現，可以優雅的解決這個問題
date: "2022-02-07T10:17:57.107Z"
categories: javascript
keywords: []
tag: javascript promise
---

![](/img/1__YpWbqiMz5wNQIfM09GRnAA.jpeg)

以往在處理非同步流程時，如果要確保執行順序並且帶入先前執行的結果會採用 callback 的方式，但很容易就會變成 callback hell 回調地獄(如下圖)，不易閱讀之外也很難維護，而 Promise 的出現，可以優雅的解決這個問題

![](/img/1__9yiIpaZcU4Dc1Tip6KBvbg.png)

一個簡單 Promise 結構如下，先用 new 建立一個 Promise 物件，就可以使用 then()來接續之後要做的事情，有點像 jQuery 那樣的鏈式寫法，一個接一個，因此就可以將原本複雜的巢狀結構變成扁平化，Promise 會傳入兩個函式 resolve 和 reject，執行 resolve 會將 Promise 的狀態改為執行成功(fullfilled)，執行 reject 的話 Promise 的狀態則會變成執行失敗(rejected)

#### Promise 的三個狀態

Promise 擁有三個狀態: 等待中（Pending）、執行成功(Fulfilled)、執行失敗(Rejected)，一但狀態從等待中變成成功或是失敗就不能再異動

![](/img/1__eWEqIUtLKDIxEyjhY__F1Sg.png)

- pending (等待中): Promise 的初始狀態
- fullfilled (已完成):  執行 resolve()，Promise 狀態會變成 fullfilled
- rejected(已拒絕):  執行 reject()，Promise 狀態會變成 rejected

#### Promise.then()

then()可以傳入兩個參數，第一個是執行成功(onFulfilled)的函式，第二個是執行失敗(onRejected)的函式，執行成功的函式會在 promise 物件的狀態變成 resolved 的時候執行，執行失敗的函式則是在 Promise 物件的狀態變成 rejected 的時候執行

一個 Promise 執行完畢之後會 return 一個 Promise，並將執行結果拋給 then()來接收，就可以做後續的處理，如果 then()不是 return Promise 而是 return 一般的值，then 就會把這個值當作是成功的結果拋給下一個 then

以下圖的例子來說，Promise.then 接收到 resolve 的結果，透過 then()逐一做加工(加上年齡、性別等等)，最後可以得到一個完整的物件

![](/img/1__wV8ug80CFUXrtVL5nUWjnw.png)

#### Promise.catch()

假設在.then 的階段發生異常該怎麼處理呢?就像 try catch 那樣，可以用 catch 來捕捉錯誤，以下面的例子來說，刻意在 then 的執行成功函式裡拋出一個錯誤，就會進到 catch 裡面

#### Promise.finally()

無論 promise 物件的狀態是成功失敗，或是有意外的錯誤都會執行 finally 裡面的函式

#### Promise.resolve 和 Promise.reject

> Promise.resolve(value) 方法回傳一個以 value 判定結果的 [Promise](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise) 物件。若 value 是個 thenable (例如，具有 ["then"方法](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise/then))，則回傳的 promise 將依其結果採取其最終狀態；若 value 是 promise，則作為呼叫 Promise.resolve 之結果；其他情形都將回傳以 value 實現的 promise。(引用自 MDN)

簡單來說使用 Promise.resolve 的語法可以直接產生一個 Fulfilled 的 Promise 物件，只會進入執行成功(onFulfilled)的函式，反過來說 Promise.reject 就只會執行失敗(onRejected)的函式

#### Promise.all()

假設我們需要等待多個非同步執行完的結果，才能做後續的處理，就可以使用 Promise.all()，Promise.all()接受一個陣列參數，陣列裡可以帶入多個 Promise 物件，當全部的 Promise 都 resolve 的時候就會執行 then 方法，如果其中有一個 Promise reject 或是發生錯誤的話就會執行 onRejected 函式，可以想成是跟團旅行，有的人會提早到，有人則是會遲到，導遊必須等到所有人都到齊了才會出發(then)，萬一有人因故未到(reject)，這個行程就會無法成行(onRejected)，假設前端的畫面呈現是需要依賴多隻 api 的回傳結果，就很適合用 Promise.all()來處理

#### Promise.race()

和 Promise.all()不同之處在於 Promise.race()只要有一個 Promise 執行 resolve 或是 reject，就會進入到 then()，並且只會回傳第一個 Promise 物件的結果，就好比說有一場馬拉松比賽，只有冠軍一個獎項，因此只要有一個選手抵達終點比賽就結束了，不過說真的在工作實務上還沒有用過 Promise.race()就是了，目前看到應用的例子是假設後端 api 三秒過後都還沒有回應，就跳出 api 回應逾時的提示

#### catch v.s onRejected

在查詢與 Promise 相關的資料時，發現當 Promise 執行 reject()時，有人會用 catch 來捕獲錯誤，有人則是用 onRejected 的函式，那假如 onRejected 函式和 catch 同時並存會發生甚麼事呢? 稍微測試了一下，發現當 Promise 執行 reject()僅會執行 onRejected 函式並不會進到 catch，真的是越看越困惑 😂

後來終於了解兩者的差異在哪裡

- **catch**: 所有 promise 的錯誤或是 reject 都能處理
- **onRejected**: 僅能處理 reject

如果今天只是單純要處理 reject 的後續行為，用 onRejected 或是 catch 都能達成，但如果是要捕獲在 onFulfilled 或是 onRejected 發生的錯誤，就只有 catch 能辦到，可以想成 catch 是比較萬能的!

經過這一系列的練習實作，總算對 Promise 的認識又上一層樓了，也順便釐清了某些一知半解的問題，像是 onRejected 和 catch 的差異，如果沒有實際寫一次還真的會以為兩者差不多 😂，以上就是關於 Promise 的簡單介紹，如果發現有錯誤歡迎指正
