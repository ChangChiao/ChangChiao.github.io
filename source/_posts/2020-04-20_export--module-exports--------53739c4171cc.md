---
title: export 跟module exports 有什麼差異？
date: "2020-04-20T15:24:30.625Z"
description: >-
  不得不承認剛開始接觸的時候，我會把export 跟module exports
  搞混(都有個export嘛)，其實他們兩個是完全不相干的，搞不懂為甚麼不能在vue.config.js使用import，後來查資料才發現兩者遵循的規範是不一樣的，既然要比較差異，就先個別介紹一下。
categories: javascript
keywords: []
tag: javascript
---

![](/img/1__eJNVgN8k3a3ydx0J5DLY__g.jpeg)

不得不承認剛開始接觸的時候，我會把 export 跟 module exports 搞混(都有個 export 嘛)，其實他們兩個是完全不相干的，搞不懂為甚麼不能在 vue.config.js 使用 import，後來查資料才發現兩者遵循的規範是不一樣的，既然要比較差異，就先個別介紹一下。

**export(** **ES6)**

- export 對外輸出（模組、變數、函式）單個檔案裡可以有多個 export
- import 內部引用（模組、變數、函式）單個檔案裡僅會有一個 export default

```javascript

//a.js

export let user ='andy'

export run = () => {console.log('run!!!')}

可以透過{}來 import 想要引用的對象

//b.js
import {user, run} from '/.a'
run()
```

也可另外設定別名

```javascript
//b.js
import { user as name, run as rush } from "/.a";
rush();
```

有時需要輸出多個對象，直接用\*一勞永逸！

```javascript
//b.js
`export * from './a';`;
```

#### export default

注意：export default 為模組的預設輸出，所以只會有一個，如果不小心寫了兩個 export default 就會報錯:`only one default export allowed per module`。

```javascript
//a.js
const list = ["olive", "steelblue", "lightpink"];
export default list;
```

因為 export default 的關係，import 的名稱可以任意定義，且不需要用{}

```javascript
//b.js
import data from "./a";
```

**module exports (CommonJS)**

那就要先介紹 node module 了，是遵循 CommonJS 的規範，每個 js 檔案都是獨立的 module，檔案裡的變數、類別，函式都是私有，除非 module.exports 導出給其他模組使用。

```javascript
//a.js
var data = {
  age: 30,
  name: "sherry",
  salary: 81000,
};

module.exports = data;
```

透過 require 來引用 module

```javascript
// b.js
var info = require("./a.js");
```

歸納一下重點

- `require`: Node.js 和 ES6 都支援的引入
- `export`、 `import` : 只有 ES6 支援的導出引入
- `module.exports`、 `exports`: 只有 Node.js 支援的導出

備註：import 經由 babel 編譯後還是會被轉成 require

參考資料：[https://segmentfault.com/a/1190000010426778](https://segmentfault.com/a/1190000010426778)
