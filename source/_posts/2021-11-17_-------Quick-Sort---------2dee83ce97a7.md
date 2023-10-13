---
title: '[排序演算法]Quick Sort — 快速排序法'
description: >-
  在了解快速排序法的概念之前要先理解partition演算法，不過單用文字敘述還是蠻抽象的，所以搭配示意圖來做說明，假如現在有個陣列[2, 6, 3, 9,
  1, 5]
date: '2021-11-17T09:24:49.935Z'
categories: []
keywords: []
slug: >-
  /@joe-chang/%E6%8E%92%E5%BA%8F%E6%BC%94%E7%AE%97%E6%B3%95-quick-sort-%E5%BF%AB%E9%80%9F%E6%8E%92%E5%BA%8F%E6%B3%95-2dee83ce97a7
---

![](/Users/joectchang_mac/Downloads/medium-export-a/fail/md_1697158079610/img/1__NtPpL2tOkxZrMgr753b0FQ.jpeg)

在了解快速排序法的概念之前要先理解partition演算法，不過單用文字敘述還是蠻抽象的，所以搭配示意圖來做說明，假如現在有個陣列\[2, 6, 3, 9, 1, 5\]

1.取陣列的最後一個值5作為pivot基準值

![](/Users/joectchang_mac/Downloads/medium-export-a/fail/md_1697158079610/img/1__22nlMmq9L28GbHpyNDGV3g.png)

2.接下來從index 0開始， 2小於5所以標記為綠色

![](/Users/joectchang_mac/Downloads/medium-export-a/fail/md_1697158079610/img/1__oZA15PuDgHrHqerqKAG2GA.png)

3.再來是6，6大於5所以標記為橘色

![](/Users/joectchang_mac/Downloads/medium-export-a/fail/md_1697158079610/img/1__xCXOR__LqThX2Aa__6uBc63Q.png)

4.輪到3，發現比5小所以把3標記為綠色，因為要把比5小的值都集中到左邊，所以跟6交換位置

![](/Users/joectchang_mac/Downloads/medium-export-a/fail/md_1697158079610/img/1__khmDqk3tN__UPn8u__4TDksQ.png)

5.接下來是9， 比5大標記成橘色

![](/Users/joectchang_mac/Downloads/medium-export-a/fail/md_1697158079610/img/1__lJILZJyxBvHRG4XYgrOO8Q.png)

6\. 輪到1，比5小所以把1標記為綠色，因為比5小的值都要集中到左邊，所以要跟6交換位置

![](/Users/joectchang_mac/Downloads/medium-export-a/fail/md_1697158079610/img/1__JOPMZMKBY42rQHPm2v3fsw.png)

7.最後再讓支點5與最大值群組(橘色的便條紙)中的第一個值做交換，5這個數字就完成了排序

![](/Users/joectchang_mac/Downloads/medium-export-a/fail/md_1697158079610/img/1__cIV2bEP__COrABnA3fe0NPg.png)

8\. 不過這時你會發現綠色的陣列並沒有由小到大排序好，這邊假設我們也不知道橘色陣列是否有排序好， 所以接下來也對左右兩個陣列，利用遞迴的方式持續做一樣的動作。

![](/Users/joectchang_mac/Downloads/medium-export-a/fail/md_1697158079610/img/1__T8rEDRvLLSdk6VZBWPDDDA.png)

函式內部實作為:

*   先將陣列裡的最後一個元素定義為基準值
*   接下來傳入起始點和終點來指定迴圈跑的範圍
*   每個遍歷的值跟基準值做比較，假如比基準值小的話就跟基準值較大的群組中最左邊的那個值交換位置，這個動作是要將比基準值小的數字都集中在左邊

進行完一輪之後，若左群體\[比基準值小的陣列\]或右群體［比基準值大的陣列］的長度大於1的話，則再次呼叫quickSort函式(遞迴)，最後將\[比基準小的陣列\] 、基準值、［比基準大的陣列］依序組合起來就是排序好的陣列。

![](/Users/joectchang_mac/Downloads/medium-export-a/fail/md_1697158079610/img/1__vOIXAQAk5pDLDqz7__47zvQ.png)

用js實作快速排序法

第二種解法比較容易理解

函式內部實作為:

*   先將陣列裡的最後一個元素定義為基準值 5
*   接下來準備兩個陣列(比基準值大的陣列\[A\] 、比基準值小的陣列\[B\])
*   跟基準值做比較， 比基準值大的值就放入A陣列 ，反之放入B陣列

若A or B陣列長度大於1 ，則再次呼叫quickSort函式(遞迴)，最後將\[比基準小的陣列\] 、基準值、［比基準大的陣列］依序組合起來就是排序過後的陣列。

![](/Users/joectchang_mac/Downloads/medium-export-a/fail/md_1697158079610/img/1__pyTGPcNZC6sAEAiP__pxBPw.png)

用js實作第二種快速排序法

👍在最差的情況下， 時間複雜度是O(n²)

👎在最佳的情況下 ， 時間複雜度是**O(n log n)**

🤚在平均情況下，時間複雜度為 O(n)\*O(log n) = O(n log n)