---
title: 我們是甚麼關係? 關於JavaScript原型鏈 — prototype chain
description: >-
  還記得剛開始學習原型和原型鏈的時候常常搞得暈頭轉向的，看了很久還是沒搞懂__proto__跟prototype之間的關聯是甚麼，那時候會看不懂的原因，不外乎我忽略了一件很重要的事，那就是JavaScript是怎麼實現繼承這件事的…
date: "2022-01-19T06:53:26.205Z"
categories: []
keywords: []
slug: >-
  /@joe-chang/%E6%88%91%E5%80%91%E6%98%AF%E7%94%9A%E9%BA%BC%E9%97%9C%E4%BF%82-%E9%97%9C%E6%96%BCjavascript%E5%8E%9F%E5%9E%8B%E9%8F%88-prototype-chain-d60e77b69649
---

![](/img/1__8__uN9NeWKSWXC23TdNtqZA.jpeg)

還記得剛開始學習原型和原型鏈的時候常常搞得暈頭轉向的，看了很久還是沒搞懂\_\_proto\_\_跟 prototype 之間的關聯是甚麼，那時候會看不懂的原因，不外乎我忽略了一件很重要的事，那就是**JavaScript 是怎麼實現繼承**這件事的? 也許你腦海裡馬上浮現 ES6 的 class 語法，先宣告一個父類別 class 然後再用子類別 extend 就可以創建出實例，這樣不就實現繼承這件事情了嗎? Java 也是這樣寫的，但其實 JavaScript 中的 class 不過是語法糖， Java 與 JavaScript 實現繼承的原理其實是大不相同的，兩者差異如下

**Java —  基於類別(Class-Based)：**

擁有「類別」與「實例」的概念，類別定義了某種物件的屬性，而實例是由類別產生的物件

**JavaScript —  基於原型(Prototype-Based)：**

沒有類別與實體的概念，它只有物件，新物件在初始化時以原型物件為範本獲得屬性

> 類別(class)和實例(instance)光聽很抽象，其實可以把類別想像成是設計稿，而實例就是依照設計稿製造出來的物件，例如汽車就是根據汽車設計藍圖設計出來的

理解了**Class-Based 和 Prototype-Based**的差異之後再來看 MDN 對於原型架構的程式語言的說明就會更清楚了

> 傳統的 OOP (Ex Java)都是先定義了類別，接著在建立物件實例之後，在類型上定義的所有屬性與函式均複製到此實例。但 JavaScript 不會複製這些屬性與函式，而是在**物件實例與其建構子之間設定連結** (原型鍊中的連結)，只要順著原型鍊就能在建構子之中找到屬性與函式。(引用自 MDN)

簡單來說就是物件是透過原型鏈來連結建構函式，建立連結之後，實體就可以使用建構子的屬性和方法了，換個比較白話的說法，小明的祖先是賽亞人，但是小明本人(物件)不會龜派氣功，但是小明擁有賽亞人(建構函式)的基因(prototype)，而這個基因是透過血緣關係(\_\_proto\_\_)串連起來的，也就是說小明的原型鍊 \_\_proto\_\_ 指向賽亞人的原型(prototype)，即便小明本人沒有這項能力，也可以使出龜派氣功的招式!

相信大家都看過下面這張圖，第一次看到這張原型鏈圖的感想應該都是有看沒有懂吧  ? 為了更理解原型鏈的流程，接下來會實作一次 JavaScript 的繼承，將這個抽象的概念一一抽絲剝繭

![](/img/1__nsLXm7WwYGFfsERPtGELpQ.png)

先建立一個建構函式 Person，並且透過 new 來創建實體(instance)，定義 name 和 age 屬性

> 透過 new 來呼叫 Person 函式的話，JavaScript 就會將 Person 視為構造函式

使用 new 創立出實體的時候，會執行下述的流程:

1.創建一個新物件

2\. 將 person1.\_\_proto\_\_指向 Person.prototype，建立原型鍊

3\. 將建構式(Person)的 this 指向 person1

4\. 回傳這個物件

#### prototype 裡面有甚麼?

![](/img/1__M3eAJ2RYf0RHZm7LUEWxVw.png)

prototype 會是一個物件，裡面會有 constructor 屬性，有趣的是 constructor 是指向 Person 本身

![](/img/1__R8JvM6b5zRwNPBF0umev7g.png)

#### prototype 的實際應用

接續剛剛的例子，我們在 Person 這個建構函式上再新增一個 sayHi 的函式，然後建立兩個物件分別是 person1 和 person2，兩個物件都能成功呼叫 sayHi 這個函式

不過這樣寫會有個問題，person1 和 person2 的 sayHi 並不會相等，那是因為兩個物件的記憶體位置不同，意謂著宣告越多物件的話，就會佔用越多記憶體，因此我們應該要將這個方法定義到 Person 的原型(prototype)上，讓所有的實例都可以使用這個方法

#### 原型鍊是怎麼運作的?

為甚麼物件 person1 能夠透過原型鏈去呼叫建構函式 Person 的 sayHi 方法呢? 背後的運作機制流程如下:

1.  person1 呼叫 sayHi 這個方法 ，不過 person1 本身沒這個方法

2\. 依照 javaScript 的機制透過\_\_proto\_\_往上找

3\. person1 的\_\_proto\_\_指向了 Person 的 prototype，也就是 person1.\_\_proto\_\_ = Person.prototype

4.如果 Person 的 prototype 也沒有 sayHi 方法就再往上找查找，直到找到最上層\_Object.prototype.\_\_proto\_\__ (\_null_)就會終止

原型鍊的串聯關係可以參考下面這張流程圖

![](/img/1__LmzG2VLxHxgqYireP6fyYQ.png)

\_\_proto\_\_ 我會想像成「血緣關係」，物件可以透過\_\_proto\_\_去追溯自己的祖先是誰，而這一連串的\_\_proto\_\_ 就是所謂的原型鍊

![](/img/1__5lekiQHwaYrBYrdvNxfhow.png)

之前在 MDN 查陣列的操作方法都搞不懂為甚麼要寫 Array.prototype.xxx()，現在就知道原因了，其實陣列本身並沒有這些方法，是陣列的原型 prototype 擁有這些操作方法，我們才能夠便利的使用 map、filter 等 function

![](/img/1__fzmFKA3jWcHlO9i0cqBSuw.png)

#### 同場加映 👈

#### `**Object.create**`

透過 Objetc.create 可以建立一個新物件，並且將傳入的物件指定為新物件的原型 ，將 person1 的\_\_proto\_\_指向 person 的 prototype，因此 person1 能夠呼叫 sayHi 方法

#### ES6 的語法  Class

讓我們用 class 的方式來將先前的範例重構一下，定義好 class Person 再讓 person1 extend，就可以實現繼承

老實說原型鍊在工作實務上不常用到，不過在面試的時候總是會被考上幾題，因此強烈建議花一點時間來理解，先前也看過了不少與原型鏈相關文章，但因為概念實在太抽象，依然似懂非懂，因此花了一些時間來實作才對原型鏈有比較深入的認識。

參考資料

- [面試官最愛考的 JS 原型鏈](https://maxleebk.com/2020/07/25/prototype/)
- [該來理解 JavaScript 的原型鍊了](https://blog.techbridge.cc/2017/04/22/javascript-prototype/)
- [MDN —  繼承與原型鏈](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)
