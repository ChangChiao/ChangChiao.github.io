---
title: 你的this不是你的this — 關於JavaScript的this指向
description: >-
  相信在大家在學習JavaScript的時候常會踩到一個坑，那就是搞不清楚this的指向到底是誰，以為是物件結果卻是window，或是把物件的方法做為callback
  function執行，結果callback…
date: "2022-01-25T05:49:49.020Z"
categories: javascript
tag: javascript
keywords: []
---

![](/img/1__IHkXB8NWvfzb3kNtRoca9w.jpeg)

相信在大家在學習 JavaScript 的時候常會踩到一個坑，那就是搞不清楚 this 的指向到底是誰，以為是物件結果卻是 window，或是把物件的方法做為 callback function 執行，結果 callback function 的 this 居然變成 window，諸如此類的問題讓人十分困擾，雖然說已經工作一陣子，但偶爾還是會被 this 雷到，因此決定寫一篇文章來釐清觀念，關於 this，大部分的情況可以用一句話來概括「**誰呼叫了這個函式，this 就會指向誰**」，當然也會有例外，這部分稍後會提到，接下來會舉出 this 的各種情境，來探討 this 的指向會有哪些可能性

### Simple call([簡易呼叫](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Operators/this#%E7%B0%A1%E6%98%93%E5%91%BC%E5%8F%AB "Permalink to 簡易呼叫"))

直接調用函式的話，在瀏覽器中 this 就會指向 window，在 node.js 環境中 this 指向 global

![](/img/1__GV6iYD__peUf7NKrGtCThBQ.png)

如果是嚴格模式，那麼 this 就會是 undefined

![](/img/1__rEkO__GJRgQLYNaXiptLx2Q.png)

額外補充一點，如果是用 var 宣告的變數，會被定義到 window 上，let 和 const 則不會有這問題

![](/img/1__hal90JOZQsHUxFlAoWKC4w.png)

#### DOM**物件 (**DOM event handler**)**

當 function 被當作參數傳入 addEventListener 的時候，其 this 的指向就是被監聽的 DOM 元素

![](/img/1__06pPsnXdV87A__DLeE4Zdsw.png)

#### 物件方法(object method)

物件方法就是定義在物件裡面的函式，呼叫物件方法時 this 指向物件本身

![](/img/1__KMpeC__w6LRZfcH7rx3jLBQ.png)

將全域的 globalLog 函式指定給物件的 log，由於呼叫函式的對象是 obj，因此 this 指向的是 obj 本身

![](/img/1__dLu3MwTFEdbU582HEzfjDg.png)

接著介紹另一種情況，先宣告一個變數 log2，並且把 obj 的 log 方法指定給 log2，執行 log2 函式， 會發現印出來的 name 會是 global，為甚麼 this 指向變成 window 了? 因為執行 log2 函式就等同是執行 window.log2，因此 this 指向 window

![](/img/1__Gy2T__s8Q4j9XDQwd6EgHVA.png)

#### 回呼函式 (**callback function)**

將 obj 的 log 方法當作 callback function 傳入，this 預期應該要是 obj，怎麼會是 window ? 因為 callback 這個函式是定義在 window 底下，由 callback 去呼叫 obj.log，this 的指向就會變成 window，但這絕不是我們想要的，為了解決這個問題，接下來要介紹 bind

![](/img/1__RStGGuVI7MTyIH6mpGjZ5w.png)

#### bind

bind 的功能就是將原本物件的 this 跟指定的物件做綁定，使用 bind 來綁定物件並不會馬上執行，而是會回傳一個函式，執行函式的當下會改變 this 的指向，讓我們用 bind 來改寫上面的函式，將 log 方法綁定 obj 物件後，this 就會是預期的 obj

![](/img/1__Vz3e5FHe2IWlnZLu79MjBA.png)

而且 bind 僅能綁定一次，從下圖可以驗證這件事

1.  log 函式使用 bind 綁定 obj1，並且將結果指定給 log1 變數，log 函式的 this 指向改為 obj1
2.  log1 函式已經綁定過 obj1 了，再次綁定 obj2 會沒有效果，log1 的 this 依然是 obj1

![](/img/1__hk74puiRvtZqJPRqM__qcrQ.png)

#### apply、 call

apply 和 call 都可以傳入兩個參數，第一個參數就是物件，目的是將該函式的 this 綁定成該物件，兩者的差異在於第二個參數，apply 接收一個陣列為參數，call 則是接受多個參數

![](/img/1__tudlxnSk92JkT__NtF1DSrw.png)

假設 call 方法傳入了 null 或是 undefined，this 會指向 window

![](/img/1__GLJ3X29QgzAHiJ__4MTAf1w.png)

#### 建構函式(function constructor)

使用建構函式 new 出來的物件，this 的指向就會是該物件，因此印出的 this.name 就會是 Jack，如果想要探究建構函式做了哪些事情，建議可以閱讀[這篇](https://medium.com/coding-hot-pot/%E6%88%91%E5%80%91%E6%98%AF%E7%94%9A%E9%BA%BC%E9%97%9C%E4%BF%82-%E9%97%9C%E6%96%BCjavascript%E5%8E%9F%E5%9E%8B%E9%8F%88-prototype-chain-d60e77b69649)

![](/img/1__fsPMpFkJ3tVMAZs____VuFIA.png)

#### 定時器

將 obj.log 方法做為 setTimeout 的第一個參數，由於 setTimeout 是 window 的方法，因此 this 就會指向 window

![](/img/1__fwAMVGJ9__I7THtYdUzrvOg.png)

#### arrow function(箭頭函式)

必須說箭頭函式的出現，讓 this 又變得更撲朔迷離 😂，因為它沒有自己的 this，跟上述大部分的例子都不相同，它的 this 是**被宣告當下所在環境的 this**，箭頭函式在 log 裡面宣告，而 log 的 this 指向 obj，因此箭頭函式的 this 指向 obj

![](/img/1__1YM28THIg8deXx3__e1vo__g.png)

> 額外補充  :

> 1.因為箭頭函式沒有 this 的緣故，所以無法做為建構函式

> 2.`call`、`apply`、`bind` 均無法改變箭頭函式中的 this

為了避免許多奇形怪狀的 this 問題，用箭頭函式會省事很多，為甚麼這麼說  ? 看看以下例子就可以明白了，以往為了解決 this 指向問題，還要多寫一個 let that = this 來獲取正確的 this

![](/img/1__SYM8JHr77OTpYqsx6HXgRw.png)

改用箭頭函式就可以解決這個問題，乾淨俐落而且方便許多

![](/img/1____lO7zp49BwEHQs6lod1dpg.png)

#### 結論

- 「**誰呼叫了這個函式，this 就會指向誰**」→ 大部分的情況
- 「**在哪個地方宣告這個函式，this 就會指向那個地方的 this**」 → 箭頭函式

基本上掌握了上述的原則，this 的指向就會是有跡可循的

參考資料

[淺談 JavaScript 頭號難題 this：絕對不完整，但保證好懂](https://blog.techbridge.cc/2019/02/23/javascript-this/)

[MDN this](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Operators/this)
