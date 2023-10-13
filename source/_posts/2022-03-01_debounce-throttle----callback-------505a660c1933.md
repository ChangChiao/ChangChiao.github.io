---
title: debounce與throttle — 讓callback不再那麼忙碌
description: >-
  你是否在開發曾經遇過一個情境，在監聽resize、scroll、input的事件上，其實只是想要某一階段的結果或是短時間只希望觸發一次，但通常都會變成頻繁的事件觸發，這時就是debounce和throttle登場的時候了，分別來看看debounce和throttle的使用情境吧……
date: "2022-03-01T03:40:47.345Z"
categories: javascript
keywords: []
---

![](/img/1__LB6PdTqHDDzWcLVdCly91g.jpeg)

你是否在開發曾經遇過一個情境，在監聽 resize、scroll、input 的事件上，其實只是想要某一階段的結果或是短時間只希望觸發一次，但通常都會變成頻繁的事件觸發，這時就是 debounce 和 throttle 登場的時候了，分別來看看 debounce 和 throttle 的使用情境吧!

#### debounce

在短時間內將數個重複觸發的事件合併為一個，避免多次不必要的觸發，節省資源，假設現在要做一個公車搜尋功能，PM 希望可以依據使用者輸入的值做搜尋，不需要再按下搜尋按鈕就可以看到搜尋結果，第一個想法就是每次使用者輸入一個字就 call 一次 api，這個做法確實可行! 不過同時也會發現一個問題，其實使用者只是想搜尋 214，結果在輸入的過程中分別打了三次 api，分別是 2、21、 214，前面兩次的搜尋都是無意義的 api 請求，該怎麼優化這個部份呢  ? 這時就需要 debounce 登場了!

![](/img/1__77xCMEVcjcH7kHSpGbMJ__g.gif)

debounce 的目的是即使在一定的時間內觸發多次，但還是只觸發一次事件，用比較白話的說法可以比喻成小明在餐廳工作遇到一個奧客，一直修改點餐單，讓小明非常困擾，於是小明想出了一個解決辦法，設定一個時間區間 10 分鐘，在 10 分鐘內奧客都不改點餐單的話，小明就請廚房出餐，反之，如果奧客在 5 分鐘的時候又改了一次菜單，代表奧客還沒確定自己要吃甚麼，那麼小明會重新計時 10 分鐘，等於是每次改點餐單都會往後延 10 分鐘，回到程式面來討論，在實作 debounce 的時候利用 setTimeout 來做到延後執行，假設設定的觸發的等候時間為 500 毫秒，一開始先觸發一次就會建立一個計時器，在 200 毫秒又觸發 1 次的時候就會將先前的計時器清空，重新開始計時 500 毫秒，直到 500 毫秒過了才會執行 callback function

> 小補充: 甚麼是 …args ? 想知道的朋友可以閱讀  — [Arguments 物件](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Functions/arguments)

因此我們就可以針對 input 監聽 change 事件，當欄位的輸入值改變了就會觸發 debounce，延後事件的觸發，因為 debounce 是回傳一個 function，所以可以有兩種寫法如下

#### throttle

由於越來越多人用行動裝置瀏覽網頁，因此蠻多網頁都設計成無限滾動 ( Infinite scrolling )的方式讓使用者瀏覽，也就是當網站已經滑到底部，會 call api 抓取更多資料，讓使用者可以用滑的方式瀏覽更多內容

![](/img/1__2d6d8rCR7A4TC2z5N0k2pQ.gif)

要實作無限滾動的話，就需要針對這個列表監聽 scroll 事件，如果在 scroll 事件的函式中有印 `console.log` 的話，會發現只是輕輕地滑一下頁面，就會觸發好幾次的 event，不過這樣其實蠻浪費效能的，我們的需求只是偵測是否滑到底部，其實不需要頻繁的檢查，這時就可以用 throttle 來解決這個問題，第一次觸發事件之後，n 秒之內都不會再觸發事件，以生活的例子來說，恐怖情人小明正在等遲到的另一半，他每一分鐘都打一次電話給他女朋友問她現在人到哪了，但頻繁的打電話其實意義不大，如果採用 throttle 的方式，就會變成每三分鐘打一次(其實還是很頻繁 😂)，可以省下不少電話費

回到程式面，假設我們預期的狀況是觸發第一次事件之後，五秒內都不要觸發的話，那麼可以用時間戳來比對觸發時間和當前時間的時間差，如果時間差大於五秒的話，代表已經超過限制的時間，就可以執行`callback function`，同時也會記錄新的時間戳

改寫 scroll 監聽事件，將 callback 和秒數傳入 throttle

normal 為一般的 scroll event，可以看得出來 throttle 的執行次數跟 normal 比起來少非常多，有效減少 callback function 的觸發次數

![](/img/1__S6mZNep2DjzxPryYi__EblQ.png)

眼尖的你應該會發現不論是 debounce 或 throttle，都採用了閉包的方式，目的是為了要讓這些函式能夠擁有自己的變數，並且不對外暴露，能夠避免變數不小心被外部函式異動，以上就是 debounce 和 throttle 的簡單介紹，有錯歡迎指正!
