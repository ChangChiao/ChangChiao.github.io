---
title: debounce與throttle — 讓callback不再那麼忙碌
description: >-
  你是否在開發曾經遇過一個情境，在監聽resize、scroll、input的事件上，其實只是想要某一階段的結果或是短時間只希望觸發一次，但通常都會變成頻繁的事件觸發，這時就是debounce和throttle登場的時候了，分別來看看debounce和throttle的使用情境吧……
date: '2022-03-01T03:40:47.345Z'
categories: []
keywords: []
slug: >-
  /@joe-chang/debounce%E8%88%87throttle-%E8%AE%93callback%E4%B8%8D%E5%86%8D%E9%82%A3%E9%BA%BC%E5%BF%99%E7%A2%8C-505a660c1933
---

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__LB6PdTqHDDzWcLVdCly91g.jpeg)

你是否在開發曾經遇過一個情境，在監聽resize、scroll、input的事件上，其實只是想要某一階段的結果或是短時間只希望觸發一次，但通常都會變成頻繁的事件觸發，這時就是debounce和throttle登場的時候了，分別來看看debounce和throttle的使用情境吧!

#### debounce

在短時間內將數個重複觸發的事件合併為一個，避免多次不必要的觸發，節省資源，假設現在要做一個公車搜尋功能，PM希望可以依據使用者輸入的值做搜尋，不需要再按下搜尋按鈕就可以看到搜尋結果，第一個想法就是每次使用者輸入一個字就call一次api，這個做法確實可行! 不過同時也會發現一個問題，其實使用者只是想搜尋214，結果在輸入的過程中分別打了三次api，分別是2、21、 214，前面兩次的搜尋都是無意義的api請求，該怎麼優化這個部份呢 ? 這時就需要debounce登場了!

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__77xCMEVcjcH7kHSpGbMJ__g.gif)

debounce的目的是即使在一定的時間內觸發多次，但還是只觸發一次事件，用比較白話的說法可以比喻成小明在餐廳工作遇到一個奧客，一直修改點餐單，讓小明非常困擾，於是小明想出了一個解決辦法，設定一個時間區間10分鐘，在10分鐘內奧客都不改點餐單的話，小明就請廚房出餐，反之，如果奧客在5分鐘的時候又改了一次菜單，代表奧客還沒確定自己要吃甚麼，那麼小明會重新計時10分鐘，等於是每次改點餐單都會往後延10分鐘，回到程式面來討論，在實作debounce的時候利用setTimeout來做到延後執行，假設設定的觸發的等候時間為500毫秒，一開始先觸發一次就會建立一個計時器，在200毫秒又觸發1次的時候就會將先前的計時器清空，重新開始計時500毫秒，直到500毫秒過了才會執行callback function

> 小補充 : 甚麼是 …args ? 想知道的朋友可以閱讀 — [Arguments 物件](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Functions/arguments)

因此我們就可以針對input監聽change事件，當欄位的輸入值改變了就會觸發debounce，延後事件的觸發，因為debounce是回傳一個function，所以可以有兩種寫法如下

#### throttle

由於越來越多人用行動裝置瀏覽網頁，因此蠻多網頁都設計成無限滾動 ( Infinite scrolling )的方式讓使用者瀏覽，也就是當網站已經滑到底部，會call api抓取更多資料，讓使用者可以用滑的方式瀏覽更多內容

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__2d6d8rCR7A4TC2z5N0k2pQ.gif)

要實作無限滾動的話，就需要針對這個列表監聽scroll事件，如果在scroll事件的函式中有印console.log的話，會發現只是輕輕地滑一下頁面，就會觸發好幾次的event，不過這樣其實蠻浪費效能的，我們的需求只是偵測是否滑到底部，其實不需要頻繁的檢查，這時就可以用throttle來解決這個問題，第一次觸發事件之後，n秒之內都不會再觸發事件，以生活的例子來說，恐怖情人小明正在等遲到的另一半，他每一分鐘都打一次電話給他女朋友問她現在人到哪了，但頻繁的打電話其實意義不大，如果採用throttle的方式，就會變成每三分鐘打一次(其實還是很頻繁😂)，可以省下不少電話費

回到程式面，假設我們預期的狀況是觸發第一次事件之後，五秒內都不要觸發的話，那麼可以用時間戳來比對觸發時間和當前時間的時間差，如果時間差大於五秒的話，代表已經超過限制的時間，就可以執行callback function，同時也會記錄新的時間戳

改寫scroll監聽事件，將callback和秒數傳入throttle

normal為一般的scroll event，可以看得出來throttle的執行次數跟normal比起來少非常多，有效減少callback function的觸發次數

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__S6mZNep2DjzxPryYi__EblQ.png)

眼尖的你應該會發現不論是debounce或throttle，都採用了閉包的方式，目的是為了要讓這些函式能夠擁有自己的變數，並且不對外暴露，能夠避免變數不小心被外部函式異動，以上就是debounce和throttle的簡單介紹，有錯歡迎指正!