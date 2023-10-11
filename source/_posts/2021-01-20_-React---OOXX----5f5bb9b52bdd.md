---
title: 用React 來寫OOXX小遊戲
description: OOXX小遊戲也可以說是很熱門的練習題，所以今天就用React 來寫一個OOXX的小遊戲吧！
date: "2021-01-20T00:54:49.160Z"
categories: []
keywords: []
tag: react
---

![](/img/1__DE3ezMkndLTLouwWLMTjOQ.jpeg)

OOXX 小遊戲也可以說是很熱門的練習題，所以今天就用 React 來寫一個 OOXX 小遊戲吧！

初始化先設定一個長度為 9 的陣列來記錄玩家下的位置，這邊用了 array.from({length}\*9) 就會製造出一個長度為 9 的陣列

```javascript
const [record, setRecord] = useState(Array.from({ length: 9 }));
```

但因為我沒有另外寫 callback function 去設定 ，所以實際上這個陣列就會是 [undefined,undefined,…undefined]

樣式的部分就寫在 module.scss， 托 flex 的福 ，可以很快的解決九宮格 css 的部分，切完版之後，我需要抓到玩家點擊的是第幾個格子，所以我這樣寫 ，看玩家點擊哪一個格子就把那個格子 index 帶入，看起來一切都很合理…

```html
<div onClick="{handclick(index)}"></div>
```

然後畫面就掛掉了。

紅字錯誤顯示無限迴圈 ，我沒料到這樣寫 handclick(index)就會馬上執行 ，因為 vue 這樣寫都沒事(喂)，因為 handclick function 改變了 record 的陣列，然後我的九宮格格子是依靠 record 陣列繪製出來的，再次 render 變成無窮迴圈，所以這邊記得要改成 arrow function，就可以正常運作

接者列出贏家的畫線有幾種可能， 這邊用九宮格的位置來記錄，第一格 index 為 0…最後一格為 8

```javascript
const arr = [
  [0, 1, 2],
  [3, 4, 5],
  [6, 7, 8],
  [0, 3, 6],
  [1, 4, 7],
  [2, 5, 8],
  [0, 4, 8],
  [2, 4, 6],
];
```

設定玩家 a 為圈圈，用 1 來代表，玩家 b 則為叉叉，用-1 來代表，由玩家 a 先開始，所以初始值設定為 1，玩家 a 下完之後，如果沒有勝負，則輪到玩家 b

```javascript
const [player, setPlayer] = useState(1);
```

每當玩家點擊格子， 該格子就會依照是哪位玩家 ，就放入對應的數字 1 or -1 ，並且馬上比對剛剛列出八種勝出方式的陣列，並將格子內的 ox 轉換成對應的數字加總並且取絕對值 ，如果為 3 就是有人勝出了， 中斷迴圈開始準備畫線

ex 如果 record 陣列這三個 index (0,1,2)的數字加起來為 3 or -3 就代表有人勝出了！（可以畫出最上排的水平線）

![](/img/1__QR__mIemiUTSYM4NkxIIuXQ.png)

用 canvas 來畫線，在一開始就已經建立好畫布，只是先隱藏起來，這邊用偷懶的方式， 設定一個 x、y 的陣列，大概抓個格子的中線位置，所以畫的線不會很精準，先取出獲勝的組合假設是 1、4、7 好了，抓陣列第一個數字為起點，陣列最後一個數字為終點，再利用除法和取餘數，拿到這兩個格子是位在第幾列的第幾行

![](/img/1__8jqzDzpejfCD0wc5TadZCw.png)

但是，這時又碰到一個小小的問題

利用 useRef 來抓 dom 裡的 canvas，殊不知一直抓不到 ！！ 一直是 undefined ，這時候推斷是雖然我即時將 canvas 更換為顯示的狀態，但是因為非同步的關係，下一行的 canvasRef.current 此時還是 undefined 狀態

![](/img/1__CuUYMtdRSoJ11X91ioqMQA.png)

一開始用了一個比較蠢的解法是 setTimeout(() => {})的確可以運作，但這是這樣寫用膝蓋想也知道不合理，後來查到比較正確的方式是在 useEffect 階段來取得 useRef，不過也失敗了，為什麼？因為我的 canvas 預設是隱藏，所以 useRef 抓到的就會是 null，問題好像越變越複雜，仔細想想，其實 canvas 也沒必要隱藏，只要可以讓使用者點擊到被 canvas 蓋住的格子就可以了，所以就在 canvas 身上設定 css 穿透屬性：pointer-events: none;就解決了！

完整程式碼

![](/img/1__dpbAnS6BGneB__mL9lbLIew.png)

最終成品!

![](/img/1__MTqY1GGS__O0Yf1qVfUN2NQ.png)
