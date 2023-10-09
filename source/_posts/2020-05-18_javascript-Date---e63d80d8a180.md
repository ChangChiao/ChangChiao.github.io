---
title: javascript Date物件
date: "2020-05-18T12:43:13.630Z"
categories: []
keywords: []
tag: "javascript"
---

![](/img/1__xXE__mwwSsk9gZ6wtTM2F9g.jpeg)

剛開始處理時間這塊的時候都是使用套件 moment.js ，沒仔細去研究 Date 物件，直到某天才發現自己連 new Date 都不知道是什麼（汗顏）因此花點時間來了解 javascript Date 物件。

如果要取的當前的時間可以用（這邊是取用戶端的電腦時間）

```javascript
new Date()
//Sat May 16 2020 22:49:52 GMT+0800 (台北標準時間)

建立特定時間點的時間物件，透過傳入不同格式的參數

new Date(1589641033968) //傳入 timestamp 豪秒
new Date(`'December 15, 1995 03:24:00'`) //傳入字串
new Date(2020,12,25) //傳入年月日
new Date(2020,12,25,10,20,30) //傳入年月日時分秒

//時間比較可以用>、<的運算值來比較

var today = new Date()
var otherday = new Date(2019, 5, 5)
if(today > otherday){
  console.log('today 比 otherday 更晚')
}
```

計算兩天之間的時間差

```javascript

var day1 = new Date(2020,5,1)
var day2 = new Date(2020,8,1)
var time = (day2 - day1) / (1000\*60\*60\*24)
//(一天有 86400000 秒）
console.log(time) //92 兩者間隔 92 天

- getFullYear() //取得年份 (yyyy)
- getMonth() //取得月份 (0–11) 從 0 開始算 0=1 月…11=12 月
- getDate() //取得是日期 (1–31)
- getHours() //取得小時 (0–23)
- getMinutes() //取得分鐘 (0–59)
- getSeconds() //取得秒數 (0–59)
- getMilliseconds() //取得毫秒 (0–999)
- getDay() //取得星期幾 (0–6)
- getTime() //取得從 1970–01–01 00:00:00 累計的毫秒數

```

下方這個取的是世界協調時間（Coordinated Universal Time）（時區為 0)

```javascript
-getUTCFullYear() - //利用全球定位時間（UTC）取得年份(yyyy)
  getUTCMonth() - //使用全球定位時間（UTC）取得月份(0–11)
  getUTCDate() - //利用全球定位時間（UTC）取得日期(1–31)
  getUTCHours() - //使用全球定位時間（UTC）取得小時(0–23)
  getUTCMinutes() - //使用全球定位時間（UTC）取得分鐘(0–59)
  getUTCSeconds() - //使用全球定位時間（UTC）取得秒數(0–59)
  getUTCMilliseconds() - // 使用全球定位時間（UTC）取得毫秒數(0–999)
  getUTCDay(); //利用全球定位時間（UTC）取得星期幾(0–6)
```

也可以用下列方式來設定時間

- [setFullYear()](https://www.fooish.com/javascript/date/setFullYear.html)設定年份
- [setMonth()](https://www.fooish.com/javascript/date/setMonth.html)設定月份 (0–11)
- [setDate()](https://www.fooish.com/javascript/date/setDate.html)設定日期 (1–31)
- [setHours()](https://www.fooish.com/javascript/date/setHours.html)設定小時 (0–23)
- [setMinutes()](https://www.fooish.com/javascript/date/setMinutes.html)設定分鐘 (0–59)
- [setSeconds()](https://www.fooish.com/javascript/date/setSeconds.html)設定秒數 (0–59)
- [setMilliseconds()](https://www.fooish.com/javascript/date/setMilliseconds.html)設定毫秒 (0–999)
- [setTime()](https://www.fooish.com/javascript/date/setTime.html)用 timestamp milliseconds 設定日期時間

toString(): 將 Date 轉換為年月日時分秒的字串

toISOString(): 將 Date 轉換成 ISO 8601 格式的字串回傳。

toLocaleString(): 將 Date 轉換為年月日時分秒的本地時區的字串

```javascript
var now = new Date()

now.toString()
“Mon May 18 2020 09:36:11 GMT+0800 (台北標準時間)”

now.toISOString()
“2020–05–18T01:36:11.735Z”

now.toLocaleString()
"2020/5/18 下午 7:38:17"

```

下面是一些例子的實作

- 時鐘功能

```javascript
function clock() {
    var today = new Date() var hour = today.getHours() var min = today.getMinutes() var sec = today.getSeconds() console.log(addzero(hour) + ':' + addzero(min) + ':' + addzero(sec))
}
```

```javascript
function addzero(num) {
  if (num < 10) {
    return "0" + num;
  }
  return num;
} //重覆每秒執行setInterval(clock ,1000)
```

- 倒數功能（這個倒數在效能較差的裝置上，可能不會那麼精準）

```javascript
var timeTarget = new Date(2020, 4, 17, 16, 5).getTime();
//new Date(2020,5,17,16,5) 月份是從0開始計算 所以這邊的5其實就是六月
var day = 0;
var hour = 0;
var min = 0;
var sec = 0;

function countDown() {
  var nowTime = new Date().getTime();
  var distance = timeTarget - nowTime;
  if (distance < 0) {
    console.log("時間到");
    clearInterval(timer);
    return;
  }
  day = Math.floor(distance / (1000 * 60 * 60 * 24));
  hour = Math.floor((distance - day * 1000 * 60 * 60 * 24) / (1000 * 60 * 60));
  min = Math.floor(
    (distance - day * 1000 * 60 * 60 * 24 - hour * 1000 * 60 * 60) / (1000 * 60)
  );
  sec = Math.floor(
    (distance -
      day * 1000 * 60 * 60 * 24 -
      hour * 1000 * 60 * 60 -
      min * 1000 * 60) /
      1000
  );
  console.log("倒數:" + day + "天" + hour + "時" + min + "分" + sec + "秒");
}
var timer = setInterval(countDown, 1000);
```
