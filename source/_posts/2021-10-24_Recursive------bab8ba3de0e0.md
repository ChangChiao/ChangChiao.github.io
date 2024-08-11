---
title: Recursive — 遞迴
description: 簡單來說就是函式自己呼叫自己的狀況，遞迴由兩部分組成
date: "2021-10-24T01:18:48.307Z"
categories: 演算法
tag: 演算法
keywords: []
---

![](/img/1__nimdzHf3xzdIgrbYVKFvNA.jpeg)

簡單來說就是函式自己呼叫自己的狀況，遞迴由兩部分組成

- `Recursive Case(遞迴情況)` —  當函式呼叫自己本身的情況
- `Base Case(基本狀況)` —  是當函式不再呼叫自己的條件，也就是終止的條件

遞迴提供了較為簡潔的寫法，不過一定要記得設定好終止的條件，避免進入無限循環，把記憶體吃光光。

#### **階乘 Factorial**

在學習遞迴的時候，大部分的人都會從階乘開始練習，因為大家高中數學都有學過階乘，來複習一下甚麼是階乘  ? 從 1 連續相乘到 n 為止，也就是 n! = n\*(n-1)\* …\*2\*1。

_ex. 5!=5\*4\*3\*2\*1_

用 js 實作階乘

```javascript
const factorial = (n) => {
  if (n === 1) return 1;
  return n * factorial(n - 1);
};

factorial(5);
```

遞迴在呼叫完自己後會先在原地等待，等待下一層將結果回傳，執行順序如下:

- factorial(5)會等待 factorial(4)回傳結果
- factorial(4)會等待 factorial(3)回傳結果
- factorial(3)會等待 factorial(2)回傳結果
- factorial(2)會等待 factorial(1)回傳結果

當 n = 1 的時候滿足終止遞迴的條件並回傳 1

- factorial(1)回傳 1
- factorial(2)回傳 2\*1 =2
- factorial(3)回傳 3\*2=6
- factorial(4)回傳 4\*6=24
- factorial(5)回傳 5\*24 = 120

遞迴的執行過程可以參考下圖，紅色箭頭是呼叫函式，綠色箭頭是回傳結果

![](/img/1__37p7__SSzew7tP8g0k9XhKw.png)

#### 九九乘法表

以經典的九九乘法表為例，一般我們會直覺地用雙迴圈來寫，其實用遞迴也可以寫出九九乘法表。

```javascript
//一般寫法
const print99 = () => {
  for (let i = 1; i <= 9; i++) {
    for (let j = 1; j <= 9; j++) {
      console.log(`${i}*${j}=${i * j}`);
    }
  }
};

print99();

//用遞迴(在前端群組看到hello大分享的寫法)
const print99ByRecursive = (i, j) => {
  console.log(`${i}*${j}=${i * j}`);
  if (j < 9) return print99(i, j + 1);
  if (i < 9) return print99(i + 1, 1);
};

print99ByRecursive(1, 1);
```

#### 斐波那契數列

![](/img/1__tjQML0x0NDgW__825YLHuHw.png)

經典的 fibonacci sequence 斐波那契數列就很適合用遞迴來解

**甚麼是斐波那契數列?**

> **斐波那契數列**由 0 和 1 開始，之後的斐波那契數列就是由前兩位數字相加而得出 ，也就是第 n 項為第 n-1 和第 n-2 項的總和  —  引用自 wikipedia

![](/img/1__Ec020AFw__on4585Q7cHARA.png)

ex. 若要取得數列中的第 11 個數字的話公式為: 89 = 55+34 (n11 = n10 + n9)

用 js 實作斐波那契數列

```javascript
const fibonacci = (i) => {
  if (i === 0 || i === 1) return i;
  return fibonacci(i - 1) + fibonacci(i - 2);
};

fibonacci(5);
```

單看程式碼應該會覺得很抽象，所以可以搭配下面這張圖來看，由於斐波那契數列的規則是每個數字都是前面兩個數相加的總和，畫成圖的話會發現結構很像 binary tree。

![](/img/1__ekwk2uvQYV7RbtzoAo98Cw.jpeg)

不過眼尖的你可能會發現一個問題，有些函式明明已經有執行過，卻還是重複執行，像是 fib(2) 、fib(1)，因為遞迴的本質是將問題拆解成多個小問題，而這些小問題可能會有重疊的狀況，因此並不會記得那些部分是已經運算過的，所以就會發生重複運算的問題，執行效率較差，這就是遞迴其中一個缺點。

除此之外，還可能發生 stack overflow(堆疊溢位  — stack 滿出來)的問題，在討論 stack overflow 之前，先來複習一下執行堆疊（call stack）的觀念

![](/img/1__Row__2VQCew2g5fE76DNZXA.png)

> 由於 JavaScript 是單執行緒的語言，一次只能做一件事情，所以使用具有後進先出特性的 stack 來儲存函式的執行環境，每次有新的任務加入的時候，就會像疊盤子一樣，一層一層往上疊加，JavaScript 會先處理最頂端的函式，當函式執行完並且 return 之後，該函式就會被移除，接下來處理下一個函式，直到 stack 被清空為止。

因為遞迴會不斷的呼叫自己的特性，就會一直把函式添加到 stack 裡面，如果遞迴太深層的話就會發生 stack overflow。

![](/img/1__pYLkQx__nUWkibni0AWKlYw.jpeg)

除了上述的問題，遞迴在呼叫函式的時候，為了保存當下執行的狀況，會將用到的區域變數、參數、返回位址暫存起來，因此會占用比較多的記憶體空間。

#### 尾遞迴(tail recursion)

以往的作法會將運算結果儲存成區域變數，尾遞迴則是改將運算結果當成參數傳給下層的函式，這個情況稱為尾遞迴。

假設現在有個函式的功能是累加數字，用一般的遞迴寫法如下

```javascript
function recsum(x) {
  if (x === 1) {
    return x;
  } else {
    return x + recsum(x - 1);
  }
}
```

假如執行函式`recsum(5)`，則 javaScript 的執行順序如下，函式需要等待下一個函式，直到中止條件後才開始回傳結果，然後將值一個一個加總起來

```javascript
recsum(5);
5 + recsum(4);
5 + (4 + recsum(3));
5 + (4 + (3 + recsum(2)));
5 + (4 + (3 + (2 + recsum(1))));
5 + (4 + (3 + (2 + 1)));
5 + (4 + (3 + 3));
5 + (4 + 6);
5 + 10;
15;
```

改成尾遞迴的話，變成使用傳入參數的方式來記錄當下累積的數字是多少

```javascript
function tailrecsum(x, running_total = 0) {
  if (x === 0) {
    return running_total;
  } else {
    return tailrecsum(x - 1, running_total + x);
  }
}
```

執行的順序如下，可以發現初次呼叫的時候就開始計算累加值，不需要用額外的變數去紀錄，減少 stack 的用量

```javascript
tailrecsum(5, 0);
tailrecsum(4, 5);
tailrecsum(3, 9);
tailrecsum(2, 12);
tailrecsum(1, 14);
tailrecsum(0, 15);
15;
```

在查遞迴資料的時候一直看到這句話

> 遞迴只應天上有，凡人應當用迴圈

的確一開始接觸遞迴的時候，覺得有點超出大腦的負荷，因為遞迴不像迴圈那麼直觀好懂，直到看了大量的圖文解說，才比較理解遞迴的運作機制，其實遞迴能做的事，迴圈也可以做到，而且遞迴的執行時間還比較長甚至還比較佔用記憶體空間呢!但是遞迴的寫法通常會比迴圈來的更簡潔，可讀性更好，因此還是看使用的情境來評估要用遞迴或是迴圈來處理問題。

參考資料: [尾呼叫](https://zh.wikipedia.org/wiki/%E5%B0%BE%E8%B0%83%E7%94%A8)

[what-is-tail-recursion](https://stackoverflow.com/questions/33923/what-is-tail-recursion)
