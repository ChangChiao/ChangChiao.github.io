---
title: Binary Tree —  Traversal
description: >-
  Traversal翻譯成中文就是遍歷的意思，如果要遍歷tree的每個節點的話，會有兩種方式，Breadth-First Tree
  Traversal和Depth-First Tree Traversal。
date: "2021-10-12T02:12:28.442Z"
categories: []
keywords: []
slug: /@joe-chang/binary-tree-traversal-622caed2fad5
---

![](/img/1__H7wB6FPsJcKZN1GAQV2CCw.jpeg)

Traversal 翻譯成中文就是遍歷的意思，如果要遍歷 tree 的每個節點的話，會有兩種方式，Breadth-First Tree Traversal 和 Depth-First Tree Traversal。

#### Breadth-First Tree Traversal

Breadth-First Tree Traversal 也被稱作 Level Order Tree Traversal，利用廣度優先搜尋(Breadth-First Search, BFS)的方式來遍歷每個節點，概念非常容易理解 ，就是將每一層的節點由左至右依序取出。

#### 甚麼是廣度優先搜尋?

> 從某個節點出發，接下來走訪相鄰節點，同一層的都走訪完了就往下一層

![](/img/1__uJCkOS5__NZfHhc__fIF8T0g.gif)

假設我們現在有顆 Binary Tree 如下圖

![](/img/1__J6DhzUs3CMIwCeaRQlztYw.png)

用廣度優先搜尋的方式走訪的步驟為:

1.  第 1 層 取出 10
2.  第 2 層 取出 8, 11
3.  第 3 層 取出 5, 9 ,15
4.  第 4 層 取出 2, 13, 19

![](/img/1__UCbUZK6z6ZtX8YHh__8__CwA.png)

最後取得陣列\[10, 8, 11, 5, 9, 15, 2, 13, 19\]

用 js 實作 Breadth-First Tree Traversal:

#### Depth-First Tree Traversal

採用的是深度優先搜尋(Depth-First-Search，DFS)的方式來遍歷節點，Depth-First Tree Traversal 又分為三種，這三種非常類似，但遍歷的順序不同。

- preorder —  前序
- inorder —  中序
- postorder —  後序

#### 深度優先搜尋

> **深度優先搜尋演算法**（英語：Depth-First-Search，DFS）是一種用於遍歷或搜尋[樹](https://zh.wikipedia.org/wiki/%E6%A0%91_%28%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%29 "樹 (資料結構)")或[圖](https://zh.wikipedia.org/wiki/%E5%9B%BE_%28%E6%95%B0%E5%AD%A6%29 "圖 (數學)")的[演算法](https://zh.wikipedia.org/wiki/%E7%AE%97%E6%B3%95 "演算法")。這個演算法會儘可能深的搜尋樹的分支。當節點 v 的所在邊都己被探尋過，搜尋將回溯到發現節點 v 的那條邊的起始節點。這一過程一直進行到已發現從源節點可達的所有節點為止。如果還存在未被發現的節點，則選擇其中一個作為源節點並重複以上過程，整個行程反覆進行直到所有節點都被存取為止。這種演算法不會根據圖的結構等資訊調整執行策略(引用自 wikipedia)

看完維基百科的解釋真的是似懂非懂，這邊我會想像是摸著牆在走迷宮，發現走到底了，就回頭找另一道牆繼續探索，只是靠牆的順序有所差異(先靠左或是先靠右)。

1\. PreOrder(root, left, right)

順序: 根節點 → 左節點 → 右節點

![](/img/1__8Z__De5S2QupaWB86ZaNgIg.png)

實際用 PreOrder 走訪一次 Binary Tree 的話，順序如下圖

![](/img/1____3qUbBtA__jnp5X98AP61Rg.png)

用 js 實作 PreOrder

2.InOrder(left, root, right)

順序: 左節點 → 根節點 → 右節點

![](/img/1__O__UyvaCbVl4YwqlMSGJnDA.png)

實際用 InOrder 的方式走訪一次二元樹的話順序如下圖

![](/img/1__mAN9EwFAeVfy6AcwoyErZQ.png)

用 js 實作 InOrder

3\. PostOrder(left, right, root)

順序: 左節點 → 右節點 → 根節點

![](/img/1__PvMF9LgJDFzCxGqMsDnMzg.png)

實際用 PostOrder 走訪一次 Binary Tree 的話順序如下圖

![](/img/1__Fr7V8bnsLDJur59kxfPswg.png)

用 js 實作 PostOrder

下面這張圖簡易的說明了 BFS 和 DFS 兩者的差異

![](/img/1__MY__xe85AdcbnSTmsG9uMlA.jpeg)

#### 如何在 Binary tree 中找到指定的值?

第一種方式使用遞迴，利用 Binary tree 的特性(當前節點的右子節點會比自己大 ，左子節點會比自己小)，如果目標值比當前節點還大的話就把當前節點的右節點作為參數傳入函式，反之就把左節點傳入，不斷呼叫自己，直到找到節點或是找不到就中止遞迴。

執行結果如下，找得到就回傳該節點，若找不到就回傳 null

![](/img/1__meHaK8bFrh37hq__ZKkFmcQ.png)

第二種方式用迴圈，假如目標值比當前節點還大的話，就把當前節點移動到右邊的子節點，反之則移動到左邊的節點，直到找到值或是找不到就跳出迴圈。

執行結果如下，找得到就回傳該節點，若找不到就回傳 null

![](/img/1__RaNELIwVfpr__osBXFoBPKA.png)

Binary Tree — Traversal 完整的程式碼如下(包含 tree 的建立)

#### 時間複雜度

👍 在最差的情況下， 時間複雜度是 O(n)

👎 在最佳的情況下 ， 時間複雜度是 O(1)

🤚 在平均情況下，時間複雜度為 O(log n)

不管是 Tree Traversal 或是在 tree 中尋找特定的值都是 leetcode 蠻常見的考題，只要可以掌握這些核心觀念，在解題的時候就會比較得心應手。

參考資料:[Tree Traversals](https://www.geeksforgeeks.org/tree-traversals-inorder-preorder-and-postorder/)
