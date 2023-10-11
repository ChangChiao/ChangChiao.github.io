---
title: Dynamic Programming(DP) — 動態規劃(下)
description: >-
  Dynamic Programmin的經典應用除了斐波那契數之外，還有背包問題、最短路徑問題、河內塔、LCS等等，那麼我們就試著用Dynamic
  Programming來解 leetcode的1143題  longest common subsequence (LCS)吧!
date: "2021-12-26T03:34:23.934Z"
categories: []
keywords: []
tag: Algorithm
slug: >-
  /@joe-chang/dynamic-programming-dp-%E5%8B%95%E6%85%8B%E8%A6%8F%E5%8A%83-%E4%B8%8B-5a90c13aad28
---

![](/img/1__LFHGdDxa6__RkEEgsYIt4Lg.jpeg)

Dynamic Programmin 的經典應用除了斐波那契數之外，還有背包問題、最短路徑問題、河內塔、LCS 等等，那麼我們就試著用 Dynamic Programming 來解 leetcode 的 1143 題 [longest common subsequence](https://leetcode.com/problems/longest-common-subsequence/) (LCS)吧!

#### 甚麼是 common subsequence?

由於 common subsequence 和 common substring 常常被搞混，因此先來理解這兩者的差異吧!

- common subsequence —  文字的集合出現的順序是一樣，不一定有連續性 ex abcd, abd
- common substring — **連續的**文字出現在兩個字串裡 ex ab, abc

> 因此 LCS 就是尋找出字串當中最長的 common subsequence

![](/img/1__SxopzgQOEsxYwo__1Cfe6MA.png)

假設現在有兩個字串 ANB 和 AKB，要求得 LCS 的長度的流程會如下

1.  從字串的最後一個字開始檢查
2.  如果相同則次數+1，將相同的文字去掉後繼續比較

3\. 當最後一個字不相同時，則會有兩種可能性

- A 字串的最後一個字可能與 B 字串的倒數第二個字相同
- B 字串的最後一個字可能與 A 字串的倒數第二個字相同

![](/img/1__NL4TeQfA2J2ye4dYSzncbg.png)

因此拆解成兩個子問題"A"與"AK"比較 以及"AN"與"A"比較

4.持續的分解問題…

先試著用遞迴解題

```javascript
const findLCS = (str1, str2) => {
  if (str1.length === 0 || str2.length === 0) return 0;
  if (str1[str1.length - 1] === str2[str2.length - 1]) {
    return (
      1 +
      findLCS(
        str1.substring(0, str1.length - 1),
        str2.substring(0, str2.length - 1)
      )
    );
  } else {
    return Math.max(
      findLCS(str1.substring(0, str1.length - 1), str2),
      findLCS(str1, str2.substring(0, str2.length - 1))
    );
  }
};

findLCS("ANB", "AKB"); //2
```

不過這題如果用遞迴解的話，leetcode 執行效率會非常的差，會顯示[Time Limit Exceeded](https://leetcode.com/submissions/detail/553641456/)，如下圖

![](/img/1__zAnjch92PQSShvFBmoPKwQ.png)

#### 使用 Dynamic Programming

在用 Dynamic Programming 解 LCS 的時候，通常會用矩陣圖來記錄比較的結果，如下圖，將比較的兩個字串各自作為 x 軸和 y 軸，兩兩比較找出相同的字母，並且搭配箭頭與數字來做標記

![](/img/1__AX0yw7j9aYCfz7N4tAPz3w.png)

矩陣圖的規則如下

1.  空字串與任何單字比必定不相同，因此填入 0
2.  若兩者的值不同，則比較上方和左方的格字數字大小

- 上方比較大標記為 ↑
- 左方比較大則標示為 ←
- 上方與左方的數字大小相同則統一標記為 ↑

3\. 若兩者的值相同，則取左上方的值+1，並且標記為 ↖

![](/img/1____l4xPf9RUbIWY45xf1uCtA.png)

依序填完數字就會發現，對角線的右下方數字即為 LCS 的長度，從最大的數字開始，沿著箭頭的方向走，走過的格子用黃色圈圈標記，將紅色箭頭的所代表的字母收集起來就會得到 LCS 的字串，在 js 中習慣以二維陣列來模擬矩陣圖，因此將上方的圖片轉化為二維陣列就會如下圖

![](/img/1__N7t8D1BGMV2DWEFMWoLzUA.png)

接下來試試看畫出 "ABCBDABC" 和 "BDCABAC"這兩個字串的 LCS 矩陣圖吧!

![](/img/1__1IDpSs6zgy0QYUmiUHc1mg.png)

依照箭頭方向收集紅色的箭頭就可以取得 LCS 為"BDABC"

![](/img/1__gYelOhaptP2UMiUToGHplQ.png)

用 js 的二維陣列來表示的話會如下圖

![](/img/1__s6OyOzzcjDs__BwnUpR8Tcw.png)

用 js 實作 Dynamic Programming

```javascript
let table1 = []; //紀錄數字
let table2 = []; //紀錄箭頭
let str1 = "ANB";
let str2 = "AKB";

const LCS = (str1, str2) => {
  let m = str1.length;
  let n = str2.length;
  //預先建立好格子
  for (let i = 0; i <= m; i++) {
    let arr = Array.from({ length: n }).fill(null);
    table1[i] = [0, ...arr];
  }
  table1[0].fill(0);

  for (let i = 0; i <= m; i++) {
    let arr = Array.from({ length: n + 1 }).fill(null);
    table2[i] = arr;
  }
  //依序將數字和箭頭填入表格
  for (let i = 1; i <= m; i++) {
    for (let j = 1; j <= n; j++) {
      //由於是二維陣列的比較起始點是從1開始, 所以對應的的字串index需-1
      if (str1[i - 1] === str2[j - 1]) {
        table1[i][j] = 1 + table1[i - 1][j - 1]; //左上角的格子
        table2[i][j] = "↖";
      } else if (table1[i - 1][j] >= table1[i][j - 1]) {
        //上方格子大於等於左方的格子
        table1[i][j] = table1[i - 1][j];
        table2[i][j] = "↑";
      } else {
        table1[i][j] = table1[i][j - 1];
        table2[i][j] = "←";
      }
    }
  }
  console.log("table1", table1);
  console.log("table2", table2);
  return table1[m][n]; //回傳LCS長度
};

let result = ""; //存放LCS字串

//印出LCS字串
const printLCS = (i, j) => {
  if (i === 0 || j === 0) {
    return;
  }
  //依照箭頭方向跳格子收集↖的字母
  if (table2[i][j] === "↖") {
    printLCS(i - 1, j - 1);
    result += str1[i - 1];
  } else if (table2[i][j] === "↑") {
    printLCS(i - 1, j);
  } else {
    printLCS(i, j - 1);
  }
};

LCS("ANB", "AKB"); //2
printLCS(str1.length, str2.length);
console.log("result", result); //AB
```

最後成功用 Dynamic Programming 解出 [longest common subsequence](https://leetcode.com/problems/longest-common-subsequence/) !，雖然執行時間和占用記憶體不盡理想…還有待優化

![](/img/1__NrykSzxaBGnI6PB0b1Htfg.png)
