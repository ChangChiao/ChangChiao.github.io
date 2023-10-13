---
title: 關於 JavaScript中的任務調度者 —  Event Loop事件循環
description: 為甚麼需要理解Event Loop?
date: "2022-04-05T06:18:34.988Z"
categories: javascript
keywords: []
---

![](/img/1__4BJS__Lzl3mb__eHfwP__1Vfg.jpeg)

#### 為甚麼需要理解 Event Loop?

![](/img/1__gDZGt0gkWF03rQjjWhqteg.png)

初學 js 的時候應該都曾經遇過這樣的問題，上述這段程式碼如果單純用想的來推敲 console.log 的執行順序，大部分的人會說是 a、b、c，但正確答案卻是 a、 c、 b，明明 settimeout 是設定零秒後執行，照理來說應該要是先印出 b 怎麼會先印出 c 呢? 這其中牽涉到的觀念其實就是 event loop，在理解 event loop 之前我們需要先知道一件事

> js 是單執行緒（**single-threaded**），也就是一次只能做一件事

假設瀏覽器今天真的是一次只做一件事情，一開始先請求一個 api，在等待 api 的回傳 response 的時候，什麼事情也不能做，所有的任務都在排隊，簡直就是一場大災難，使用者體驗會非常差，那麼有什麼方法可以讓瀏覽器可以跳脫 js 單執行緒的限制， 一次執行多個任務呢？就是 event loop

> event loop 讓 js 能夠在不同的 runtime(ex.node.js、瀏覽器)非同步的處理任務

#### event loop 的核心角色

分別為 call stack、web Apis、callback queue

![](/img/1__2JHhkUYho3YOej9HieOnoA.png)

#### Call stack(執行堆疊)

![](/img/1__G__cmdU3EkU3ti0YVPuI1rg.gif)

Stack 是一種資料結構，就好比一疊撲克牌一樣，如果要新增資料就從堆疊上面放入資料，如果要移除資料也是要從堆疊上方開始移除，Stack 遵循著後進先出的原則，將函式依照執行順序逐一放入 call Stack，最晚放入 Stack 的函式會被最先取出執行，那如果這個堆疊不斷地被放入函式，都沒有做移除的的話，會發生什麼事呢？想必大家都曾經看過下面這個錯誤訊息

> Maximum call stack size exceeded

原因就是不小心寫了無窮迴圈，導致函式不斷的被放入 stack，造成瀏覽器 stackoverflow(執行堆疊溢出)而顯示的警告訊息

#### Callback Queue(工作佇列)

![](/img/1__C01__vZvpJIusjV3AClGbbA.gif)

Queue 是一種資料結構，如果要新增資料會從佇列後面新增，要移除的話會從前面移除資料，Queue 遵循著後進先出的原則，當 stack 裡面沒有執行函式的時候，就會將 Queue 裡面的任務放進 stack 執行，運作模式如同上圖

#### Web APIs

瀏覽器提供的各種 API ex .DOM(document) 、AJAX(XMLHttpRequest) Timeout (setTimeout)等等，能夠非同步的處理其他任務， Web APIs 會將需要執行的 callback function 放到 queue 等待執行

#### Heap

分配函式、變數的記憶體空間，不參與 event loop

#### event loop  的運作流程

對 event loop 有了基本的認識之後，我們試著再來理解一次下面這段程式碼的執行順序吧！

![](/img/1__gDZGt0gkWF03rQjjWhqteg.png)

步驟一：將 console.log(a) 放入 call stack，執行完畢後移除

步驟二： 將 setTimeout 放入 call stack，由於是非同步函式，會先將 setTimeout 移動到 Web APIs 開始倒數

步驟三：將 console.log(c) 放入 call stack，執行完畢後移除

步驟四：當 setTimeout 倒數的時間結束，就會把 setTimeout 要執行的 callback function 放到 queue 裡面等待

步驟五：當 stack 裡面的任務都清空了，callback queue 就會把排隊的任務拿出來再放到 stack 裡面執行， 也就是 console.log(b)

所以即使 setTimeout 的秒數的秒數設定為 0 秒，都會等到 stack 裡面的任務執行完畢才會將 queue 裡的 setTimeout 的 callback 取出執行，完整的執行流程可以參考下圖

![](/img/1__64h__nr8S7ry8f__PAkBRDFg.gif)

> 額外補充：為什麼 setTimeout n 秒並不代表一定會在 n 秒後執行？因為有可能 callback queue 前面還排了一些任務，必須等這些任務都執行完畢，才能等到 setTimeout 的 callback 執行，因此 setTimeout 無法保證事件一定能夠準時執行

#### macro-task，micro-task

![](/img/1__G0fKDNdofOC5wAM43__AjHw.png)

先前面試被問到這個題目，請我講出執行順序，當場腦袋當機，我知道這些都是非同步的函式，但對於執行的順序真的毫無頭緒，如果你能夠輕鬆地說出答案，那恭喜你對於 macro-task，micro-task 非常有概念，如果不是的話，可以繼續往下閱讀

其實在非同步任務當中，還有細分為 macro-task(宏任務)，micro-task(微任務)，這兩種任務的差異在於執行的優先順序不同，在 event loop 的設計當中，會優先執行 micro-task 的任務，再執行 macro-task，因為 micro-task 處理時間比較短，因此這樣的執行順序會更有效率，macro-task 和 micro-task 的處理順序可參考下圖

![](/img/1__0xDGBNrA1WtfSfYY3FJOdw.gif)

#### macro-task

- setTimeout
- setInterval
- UI rendering
- requestAnimationFrame

#### micro-task

- process.nextTick() ( 僅限 node.js ）
- Promise
- async function

![](/img/1__D0kYLOdf75P9Vq__csDGS__Q.png)

當 stack 清空的時候，micro-task 的執行順序會優先於 macro-task，一開始會先執行 micro-task，所有的 micro-task 都執行完畢才會執行 macro-task，當執行完單個 macro-task 會檢查 micro-task 是否為空，若 micro-task queue 不為空，則會優先執行所有的 micro-task，不斷地循環，有了基本概念之後，來試著說出這道題目的執行順序吧！

![](/img/1__G0fKDNdofOC5wAM43__AjHw.png)

- 一開始遇到函式宣告可以先跳過，先遇到的是 setTimeout，setTimeout 為 macro-task，因此放入 macro-task queue，等待執行
- 倒數第二行執行了 fn2 函式，fn2 是一個 async function，await 的作用類似於  .then，await 下方的程式碼可以視作是.then 之後要做的事情，因此 console.log(e)會先放到 micro-task queue 排隊等待執行
- 接著執行 fn1， new Promise 內部的程式碼為同步執行，.then 內部的的程式碼則為非同步執行，因此會先印出 a、b，.then 裡的 console.log(c)則會放入 micro-task queue
- 將 fn1.then 裡 cosole.log(f) 放入 micro-task queue
- 同步的任務都已經執行完了，接著將 micro-task queue 裡面的任務依序執行，分別是 e 、c、f
- 最後執行 macro-task 也就是 setTimeout 的 callback function，印出 d
- 執行順序為 a、b、e、c、f、d

![](/img/1__laIUFejfHv__06tMr4VrkXQ.png)

不過實際用瀏覽器跑過一次，會發現正確的執行順序會是 a、b、c、e、f、d，為什麼執行順序不是我們想的那樣，問題就出在 await Promise.resolve 那行，必須先將 await 語法轉換為 promise 語法，會發現並非如當初所想的只有一個.then，而是有兩個，因此 e 會比 c 更晚執行

![](/img/1__xfxclBMsahaE2i9zIPyqDw.png)

正確的執行順序如下

![](/img/1__OerprrwVtkCBxRMf__4ni7w.png)

一開始在思考這類型的題目時，建議可以將每一行的程式碼細分為同步任務、micro-task，macro-task，如同上圖，先順著跑完同步程式碼，遇到 micro-task 和 macro-task 都先分類到一旁，等到同步程式碼都結束了，開始將 micro-task 依照放入的順序取出，接下來再輪到 macro-task，思緒會清晰很多，提供給大家做個參考

最後推薦的 Philip Roberts 影片，將 event loop 的觀念解釋得非常清楚
