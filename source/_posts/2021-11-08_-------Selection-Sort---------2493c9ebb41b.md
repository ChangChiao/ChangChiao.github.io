---
title: "[排序演算法]Selection Sort — 選擇排序法"
description: >-
  何謂排序演算法呢?簡單來說就是經過一連串的步驟將數字由小排到大或是由大排到小的演算法，下圖列出幾種常見的排序演算法，像是插入排序法、氣泡排序法、合併排序法等等。
date: "2021-11-08T09:55:41.095Z"
categories: 演算法
keywords: []
---

![](/img/1__PfiVmotYEyxtz2OKg1RBTQ.jpeg)

何謂排序演算法呢?簡單來說就是經過一連串的步驟將數字由小排到大或是由大排到小的演算法，下圖列出幾種常見的排序演算法，像是插入排序法、氣泡排序法、合併排序法等等。

![](/img/1__mpmbmlnUGuySa9umM5oTiw.gif)

今天要介紹的是選擇排序法，先找出數字裡面最小的或最大的數，第一個步驟是先找最小的數字，再跟尚未排序的數字的最左邊的互換。

假設現在有個尚未排序的陣列:`[ 29, 10, 14, 37, 13]`

- 尚未排序的數字: `29, 10, 14, 37, 13`
- 排序好的數字:無

選擇排序法的執行步驟:

1.  發現尚未排序的數字中 10 為最小值，與尚未排序中最左邊的數字 29 交換，此時陣列為`[10, 29, 14, 37, 13]`

![](/img/1__vtRkhQVIPCzp3aDm1zPgmA.png)

- 尚未排序的數字:`29, 14, 37, 13`
- 排序好的數字:`10`

  2.發現尚未排序的數字中 13 為最小值，與尚未排序中最左邊的數字 29 交換，此時陣列為`[10, 13, 14, 37, 29]`

![](/img/1__9Si7gDIeJOiqWYVWB1ACKQ.png)

- 尚未排序的數字有 `14, 37, 29`
- 排序好的數字:`10, 13`

以此類推，直到所有數字都由小排到大

![](/img/1__dQqcLbRo8lR0bKNwN8k3SQ.gif)

用 js 實作選擇排序法

```javascript
const selectionSort = (arr) => {
  for (let i = 0; i < arr.length - 1; i++) {
    let minIndex = i;
    for (let j = i; j <= arr.length - 1; j++) {
      if (arr[j] < arr[minIndex]) {
        minIndex = j;
      }
    }
    [arr[minIndex], arr[i]] = [arr[i], arr[minIndex]];
  }
  return arr;
};

selectionSort([29, 10, 14, 37, 13]);
```

#### 時間複雜度

👍 在最差的情況下，時間複雜度是 O(n²)

👎 在最佳的情況下，時間複雜度是 O(n²)

🤚 在平均情況下，時間複雜度為 O(n²)

參考資料:[Sorting Algorithms](https://dev.to/edwardcashmere/sorting-algorithms-2541)
