---
title: TypeScript筆記 (3) — function
description: 參數類型
date: "2022-02-12T13:07:24.022Z"
categories: typescript
keywords: []
---

![](/img/1__nnjL3PzLAl49JV6qCrROuQ.png)

#### 參數類型

可以定義傳入參數的類型為何，參數如果帶問號則代表可填可不填，如果傳入的參數非指定的型別就會跳出警告

![](/img/1__gokKuEMSjdJYP4xfBLrefQ.png)

TypeScript 會自動推斷回傳值的型別為何，也可以手動聲明函式最後會回傳的型別是什麼，如下圖的 getName 函式，括號後面接著：string，代表回傳的值會是字串型別

![](/img/1____tpVdtW8G3__1e1IUt97FKw.png)

如果回傳的陣列為複合式的值，可以推斷出為 union 的型別

![](/img/1__AJlTfbXIUKj83YdRq9rQdQ.png)

若傳入的參數類型為 type，可以用  **.** 就知道有哪些屬性可以使用

![](/img/1____v9cf1F__jqiA9eW44Wz2hA.png)

#### Never

因為拋出錯誤或是無窮迴圈的緣故，讓函式永遠不會回傳值或是回傳錯誤，就會定義為 never

![](/img/1__bDofEJ0T7xXxitgL2sBJKw.png)

#### void

void 為空值的意思，代表這個函式不會回傳任何值

![](/img/1__NZaFnVOXiXpr__EX8NJQViw.png)

#### 泛型

泛型會用<T> 表示，也就是不預先指定具體的型別，而是由使用的人自行定義型別為何

![](/img/1__mosKtIqzgLZ5aLE7NvWvrQ.png)

多個泛型可以這樣寫

![](/img/1__dXPNlbBfqMSabRCyFGH4dw.png)

泛型搭配 extends 使用，可以讓 TypeScript 推論該類型有什麼屬性或方法可以用

![](/img/1__JttXFJwbyNA9ldYTREwbKA.png)

#### Function Overload  函式超載

允許用相同的函式名稱定義可接受多個**型別不同的參數**或是**參數數量不同**的函式，overload 會分作兩個部分

- overload signatures:定義函式傳入的型別
- function implementation:實作邏輯的 function，必須滿足所有的 overload signatures

假設要用 TypeScript 模擬 css 的 margin 語法（可傳入 1 ～ 4 個參數），就可以用 overload 的方式來處理，允許函式傳入不同數量的參數

![](/img/1__9PTbQ4x__6v__wTE__xBC2EzQ.png)

坦白說第一次看到這個寫法的我反應是這樣的，好像黑魔法一樣

![](/img/1__08AmCLreCtHe__Sm9FLoxCw.png)

#### **其餘參數（rest parameter）**

以往如果需要傳入多個參數，都會乖乖的寫一個蘿蔔一個坑，不過現在可以用其餘參數的方式（…nums）來接收數量不確定的參數，並將其視作一個陣列

![](/img/1____rxpM__6__5wYIXffUlcPpyw.png)

假如要將一個變數用其餘參數的方式傳入函式會跳出警告，因為 TypeScript 無法確認傳入的陣列長度為何

![](/img/1__UZUCxut__Vd3sSISOhwHLrg.png)

該如何解決上述問題？只要將傳入的變數斷言為 const 即可，讓 typeScript 將 nums 視作唯讀屬性的 tuple(元祖）

![](/img/1__aVAIhK0Xx0rQsvprtVpPWQ.png)

#### 建構函式

![](/img/1__BMuqGR1__82xPJJesdpuRxw.png)

參考資料: [JavaScript 沒有的 Function Overloads(函式超載)](https://ithelp.ithome.com.tw/articles/10277785?sc=iThelpR)

[MDN —  其餘參數](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Functions/rest_parameters)
