---
title: 你的this不是你的this — 關於JavaScript的this指向
description: >-
  相信在大家在學習JavaScript的時候常會踩到一個坑，那就是搞不清楚this的指向到底是誰，以為是物件結果卻是window，或是把物件的方法做為callback
  function執行，結果callback…
date: '2022-01-25T05:49:49.020Z'
categories: []
keywords: []
slug: >-
  /@joe-chang/%E4%BD%A0%E7%9A%84this%E4%B8%8D%E6%98%AF%E4%BD%A0%E7%9A%84this-%E9%97%9C%E6%96%BCjavascript%E7%9A%84this%E6%8C%87%E5%90%91-d1f8cea514ff
---

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__IHkXB8NWvfzb3kNtRoca9w.jpeg)

相信在大家在學習JavaScript的時候常會踩到一個坑，那就是搞不清楚this的指向到底是誰，以為是物件結果卻是window，或是把物件的方法做為callback function執行，結果callback function的this居然變成window，諸如此類的問題讓人十分困擾，雖然說已經工作一陣子，但偶爾還是會被this雷到，因此決定寫一篇文章來釐清觀念，關於this，大部分的情況可以用一句話來概括「**誰呼叫了這個函式，this就會指向誰**」，當然也會有例外，這部分稍後會提到，接下來會舉出this的各種情境，來探討this的指向會有哪些可能性

### Simple call([簡易呼叫](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Operators/this#%E7%B0%A1%E6%98%93%E5%91%BC%E5%8F%AB "Permalink to 簡易呼叫"))

直接調用函式的話，在瀏覽器中this就會指向window，在node.js環境中this指向global

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__GV6iYD__peUf7NKrGtCThBQ.png)

如果是嚴格模式，那麼this就會是undefined

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__rEkO__GJRgQLYNaXiptLx2Q.png)

額外補充一點，如果是用var宣告的變數，會被定義到window上，let和const則不會有這問題

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__hal90JOZQsHUxFlAoWKC4w.png)

#### DOM**物件 (**DOM event handler**)**

當function被當作參數傳入addEventListener的時候，其this的指向就是被監聽的DOM元素

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__06pPsnXdV87A__DLeE4Zdsw.png)

#### 物件方法(object method)

物件方法就是定義在物件裡面的函式，呼叫物件方法時this指向物件本身

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__KMpeC__w6LRZfcH7rx3jLBQ.png)

將全域的globalLog函式指定給物件的log，由於呼叫函式的對象是obj，因此this指向的是obj本身

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__dLu3MwTFEdbU582HEzfjDg.png)

接著介紹另一種情況，先宣告一個變數log2，並且把obj的log方法指定給log2，執行log2函式， 會發現印出來的name會是global，為甚麼this指向變成window了? 因為執行log2函式就等同是執行window.log2，因此this指向window

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__Gy2T__s8Q4j9XDQwd6EgHVA.png)

#### 回呼函式 (**callback function)**

將obj的log方法當作callback function傳入，this預期應該要是obj，怎麼會是window ? 因為callback這個函式是定義在window底下，由callback去呼叫obj.log，this的指向就會變成window，但這絕不是我們想要的，為了解決這個問題，接下來要介紹bind

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__RStGGuVI7MTyIH6mpGjZ5w.png)

#### bind

bind的功能就是將原本物件的this跟指定的物件做綁定，使用bind來綁定物件並不會馬上執行，而是會回傳一個函式，執行函式的當下會改變this的指向，讓我們用bind來改寫上面的函式，將log方法綁定obj物件後，this就會是預期的obj

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__Vz3e5FHe2IWlnZLu79MjBA.png)

而且bind僅能綁定一次，從下圖可以驗證這件事

1.  log函式使用bind綁定obj1，並且將結果指定給log1變數，log函式的this指向改為obj1
2.  log1函式已經綁定過obj1了，再次綁定obj2會沒有效果，log1的this依然是obj1

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__hk74puiRvtZqJPRqM__qcrQ.png)

#### apply、 call

apply和call都可以傳入兩個參數，第一個參數就是物件，目的是將該函式的this綁定成該物件，兩者的差異在於第二個參數，apply接收一個陣列為參數，call則是接受多個參數

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__tudlxnSk92JkT__NtF1DSrw.png)

假設call方法傳入了null或是undefined，this會指向window

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__GLJ3X29QgzAHiJ__4MTAf1w.png)

#### 建構函式(function constructor)

使用建構函式new出來的物件，this的指向就會是該物件，因此印出的this.name就會是Jack，如果想要探究建構函式做了哪些事情，建議可以閱讀[這篇](https://medium.com/coding-hot-pot/%E6%88%91%E5%80%91%E6%98%AF%E7%94%9A%E9%BA%BC%E9%97%9C%E4%BF%82-%E9%97%9C%E6%96%BCjavascript%E5%8E%9F%E5%9E%8B%E9%8F%88-prototype-chain-d60e77b69649)

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__fsPMpFkJ3tVMAZs____VuFIA.png)

#### 定時器

將obj.log方法做為setTimeout的第一個參數，由於setTimeout是window的方法，因此this就會指向window

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__fwAMVGJ9__I7THtYdUzrvOg.png)

#### arrow function(箭頭函式)

必須說箭頭函式的出現，讓this又變得更撲朔迷離😂，因為它沒有自己的this，跟上述大部分的例子都不相同，它的this是**被宣告當下所在環境的this**，箭頭函式在log裡面宣告，而log的this指向obj，因此箭頭函式的this指向obj

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__1YM28THIg8deXx3__e1vo__g.png)

> 額外補充 :

> 1.因為箭頭函式沒有this的緣故，所以無法做為建構函式

> 2.`call`、`apply`、`bind` 均無法改變箭頭函式中的this

為了避免許多奇形怪狀的this問題，用箭頭函式會省事很多，為甚麼這麼說 ? 看看以下例子就可以明白了，以往為了解決this指向問題，還要多寫一個let that = this 來獲取正確的this

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__SYM8JHr77OTpYqsx6HXgRw.png)

改用箭頭函式就可以解決這個問題，乾淨俐落而且方便許多

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1____lO7zp49BwEHQs6lod1dpg.png)

#### 結論

*   「**誰呼叫了這個函式，this就會指向誰**」→ 大部分的情況
*   「**在哪個地方宣告這個函式，this就會指向那個地方的this**」 → 箭頭函式

基本上掌握了上述的原則，this的指向就會是有跡可循的

參考資料

[淺談 JavaScript 頭號難題 this：絕對不完整，但保證好懂](https://blog.techbridge.cc/2019/02/23/javascript-this/)

[MDN this](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Operators/this)