---
title: 認識Reflow和Repaint
description: >-
  效能問題對於開發者來說一直都是一項難題，畢竟無法確保每個使用者的裝置效能都有中上水平，特別是某些中低階的手機，所以就必須關注那些行為可能會是效能殺手，導致使用者無法流暢的瀏覽網頁，為了避免自己不小心埋下效能的地雷，
  就需要來了解瀏覽器的兩個機制 Reflow和Repaint
date: "2021-07-18T06:06:58.357Z"
categories: []
tag: web
keywords: []
slug: /@joe-chang/%E8%AA%8D%E8%AD%98reflow%E5%92%8Crepaint-1155e4fb5b8f
---

![](/img/1__pkAcc7Ql8ZkAqWGu____NzQg.jpeg)

效能問題對於開發者來說一直都是一項難題，畢竟無法確保每個使用者的裝置效能都有中上水平，特別是某些中低階的手機，所以就必須關注那些行為可能會是效能殺手，導致使用者無法流暢的瀏覽網頁，為了避免自己不小心埋下效能的地雷， 就需要來了解瀏覽器的兩個機制 Reflow 和 Repaint

在討論之前 Reflow 和 Repaint 之前，先來理解瀏覽器的渲染過程吧!

![](/img/1__Uj4d2rdfBo26RL__EHS8djA.png)

1. 解析 HTML 檔案，生成 DOM tree

2. 解析 CSS 檔案，生成 CSSOM

3. 將 DOM 與 CSSOM 合併為 Render Tree

(如果遇到 display:none 的元素 ，則不會被計算進去)

4. 計算每個可見元素的佈局(寬高和位置)

5. 將 Render Tree 計算結果繪製到畫面上

而 Reflow 和 Repaint 則會在第四和第五個步驟觸發

#### Reflow(**回流**)

當 Render Tree 的佈局改變，就會重新計算 DOM 的位置和大小，這個過程稱之為 Reflow，舉例來說 ， 在頁面載入完畢之後 ，刪除了某個 DOM 節點 ，這時候瀏覽器就必須重新計算佈局 。

> 頁面第一次載入的時候，一定會觸發一次 Reflow

**甚麼情況下會造成 Reflow ?**

- Window Resizing(變更視窗尺寸)
- 對 DOM 元素進行新增、 修改 、移除的操作
- 更改 CSS 的樣式(會影響佈局的): padding、 margin、 border、 width、 font-family、 font-size
- 新增或移除 DOM 的樣式
- 修改 input 的內容
- 獲取元素的特定屬性(scrollTop、scrollLeft、scrollWidth、scrollHeight、 offsetTop、offsetLeft、offsetWidth、offsetHeight、clientTop、clientLeft、clientWidth、clientHeight)

瀏覽器在處理 Reflow 這件事有個優化機制 ，會將多個 Reflow 和 Repaint 放到佇列，然後在每一個 requestAnimationFrame(每 16.6ms)清空佇列，合併為一次的處理然後更新畫面，但如果當你需要獲取 offsetTop 、offsetWidth、 scrollTop、 clientTop…這些屬性或是呼叫 scrollIntoView() 方法的時候 ，瀏覽器為了確保你拿到的是最新的值，就必須馬上觸發 Reflow 。

> Reflow 必定會觸發 Repaint

#### Repaint(**重繪**)

Render Tree 的樣式改變 ，單純改變外觀顏色，不影響佈局，稱之為 Repaint，相較於 Reflow，Repaint 的效能開銷就小很多。

**甚麼情況下會造成 Repaint?**

- 修改 DOM 元素的 CSS 屬性:background-color 、color、 opacity、 visibility

簡單來說:

需要重新計算 DOM 節點 → Reflow

更改樣式重新繪製畫面，不涉及畫面排版 → Repaint

#### 如何減少 Reflow 的次數?

- 避免用 table 排版
- 如果要對該 DOM 元素設定動畫，可以先設定 postion 為 absolute 或是 fixed，讓該元素脫離文件流，就不會影響到其他元素的佈局
- 如果要用 JS 來設定樣式的話，避免逐行修改，改用 ClassName 來修改樣式

```javascript
// bad
element.style.left = left + "px";
element.style.top = top + "px";

//good
element.className += "newStyle";
```

- 使用 DocumentFragment 來暫存對 DOM 的變更，最後再一次 apply 變更到 DOM 上 ，這樣就可以將多次的 Reflow 和 Repaint 降低為一次
- CSS 的層級避免寫得太深，因為瀏覽器需要花費更多效能去找出符合規則的元素
- 犧牲動畫的精緻度， Ex 每次移動 1px v.s 每次移動 3px ，後者雖然動畫效果較不流暢但效能會比較好
- 避免多次讀取 offsetTop、 clientTop 等屬性 ，可以用一個變數將值存起來
- 用 visibility 代替 display:none

> 在網路上看到一個很有趣的比喻  : DOM 整形就會觸發 Reflow，DOM 化妝就會觸發 Repaint。

參考資料:[DOM Performance](https://gist.github.com/faressoft/36cdd64faae21ed22948b458e6bf04d5)
