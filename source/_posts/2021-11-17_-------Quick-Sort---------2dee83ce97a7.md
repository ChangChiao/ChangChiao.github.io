---
title: "[排序演算法]Quick Sort — 快速排序法"
description: >-
  在了解快速排序法的概念之前要先理解partition演算法，不過單用文字敘述還是蠻抽象的，所以搭配示意圖來做說明，假如現在有個陣列[2, 6, 3, 9,
  1, 5]
date: "2021-11-17T09:24:49.935Z"
categories: 演算法
keywords: []
---

![](/img/1__NtPpL2tOkxZrMgr753b0FQ.jpeg)

在了解快速排序法的概念之前要先理解 partition 演算法，不過單用文字敘述還是蠻抽象的，所以搭配示意圖來做說明，假如現在有個陣列 [2, 6, 3, 9, 1, 5]

1. 取陣列的最後一個值 5 作為 pivot 基準值

![](/img/1__22nlMmq9L28GbHpyNDGV3g.png)

2. 接下來從 index 0 開始， 2 小於 5 所以標記為綠色

![](/img/1__oZA15PuDgHrHqerqKAG2GA.png)

3. 再來是 6，6 大於 5 所以標記為橘色

![](/img/1__xCXOR__LqThX2Aa__6uBc63Q.png)

4. 輪到 3，發現比 5 小所以把 3 標記為綠色，因為要把比 5 小的值都集中到左邊，所以跟 6 交換位置

![](/img/1__khmDqk3tN__UPn8u__4TDksQ.png)

5. 接下來是 9， 比 5 大標記成橘色

![](/img/1__lJILZJyxBvHRG4XYgrOO8Q.png)

6. 輪到 1，比 5 小所以把 1 標記為綠色，因為比 5 小的值都要集中到左邊，所以要跟 6 交換位置

![](/img/1__JOPMZMKBY42rQHPm2v3fsw.png)

7. 最後再讓支點 5 與最大值群組(橘色的便條紙)中的第一個值做交換，5 這個數字就完成了排序

![](/img/1__cIV2bEP__COrABnA3fe0NPg.png)

8. 不過這時你會發現綠色的陣列並沒有由小到大排序好，這邊假設我們也不知道橘色陣列是否有排序好， 所以接下來也對左右兩個陣列，利用遞迴的方式持續做一樣的動作。

![](/img/1__T8rEDRvLLSdk6VZBWPDDDA.png)

函式內部實作為:

- 先將陣列裡的最後一個元素定義為基準值
- 接下來傳入起始點和終點來指定迴圈跑的範圍
- 每個遍歷的值跟基準值做比較，假如比基準值小的話就跟基準值較大的群組中最左邊的那個值交換位置，這個動作是要將比基準值小的數字都集中在左邊

進行完一輪之後，若左群體\[比基準值小的陣列\]或右群體［比基準值大的陣列］的長度大於 1 的話，則再次呼叫 quickSort 函式(遞迴)，最後將\[比基準小的陣列\] 、基準值、［比基準大的陣列］依序組合起來就是排序好的陣列。

![](/img/1__vOIXAQAk5pDLDqz7__47zvQ.png)

用 js 實作快速排序法

```javascript
//快速排序法
const partition = (arr, left, right) => {
  let pivot = arr[right];
  //用來計算有幾個數字小於pivot
  let i = left - 1;
  //從最左邊找到pivot的前一個停止
  for (let j = left; j <= right - 1; j++) {
    if (arr[j] <= pivot) {
      i++;
      [arr[i], arr[j]] = [arr[j], arr[i]];
    }
  }
  [arr[i + 1], arr[right]] = [arr[right], arr[i + 1]];
  return i + 1;
};

const quickSort = (arr, left, right) => {
  if (left < right) {
    let sortedIndex = partition(arr, left, right);
    quickSort(arr, left, sortedIndex - 1);
    quickSort(arr, sortedIndex + 1, right);
  }
  return arr;
};

const arr = [2, 6, 3, 9, 1, 5];
quickSort(arr, 0, arr.length - 1);
//輸出 [1, 2, 3, 5, 6, 9]
```

第二種解法比較容易理解

函式內部實作為:

- 先將陣列裡的最後一個元素定義為基準值 5
- 接下來準備兩個陣列(比基準值大的陣列\[A\] 、比基準值小的陣列\[B\])
- 跟基準值做比較， 比基準值大的值就放入 A 陣列 ，反之放入 B 陣列

若 A or B 陣列長度大於 1 ，則再次呼叫 quickSort 函式(遞迴)，最後將\[比基準小的陣列\] 、基準值、［比基準大的陣列］依序組合起來就是排序過後的陣列。

![](/img/1__pyTGPcNZC6sAEAiP__pxBPw.png)

用 js 實作第二種快速排序法

```javascript
const quickSort2 = (arr) => {
  if (arr.length <= 1) return arr;
  const small = [];
  const big = [];
  const pivot = arr[arr.length - 1];
  for (let i = 0; i < arr.length - 1; i++) {
    if (pivot > arr[i]) {
      small.push(arr[i]);
    } else {
      big.push(arr[i]);
    }
  }

  return [...quickSort2(small), pivot, ...quickSort2(big)];
};

const arr = [2, 6, 3, 9, 1, 5];
quickSort2(arr);

//輸出 [1, 2, 3, 5, 6, 9]
```

👍 在最差的情況下，時間複雜度是 O(n²)

👎 在最佳的情況下，時間複雜度是**O(n log n)**

🤚 在平均情況下，時間複雜度為 O(n)\*O(log n) = O(n log n)
