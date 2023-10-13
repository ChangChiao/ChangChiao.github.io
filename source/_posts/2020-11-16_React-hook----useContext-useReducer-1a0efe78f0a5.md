---
title: React hook(中)-useContext&useReducer
description: useContext()
date: "2020-11-16T15:55:18.489Z"
categories: react
keywords: []
slug: /@joe-chang/
---

![](/img/1__au40ZgVt3x6DrLVe__ptQ9w.jpeg)

useContext 會和 React Context API 搭配使用，可以讓 component 共享資料，像是進階版的 props，不用一層一層的傳 props 真的太美好了，可以隔山打牛，可以避免掉 HOC 的波動拳…不得不說這樣真的方便多了。

#### React Context API

`父層 component 的部分：`

利用 createContext 建立 Context component，createContext 可傳入預設值，Context Provider 需要包住整個組件，Context Provider 的 value 記得要傳入需要傳遞的值，只要是被 provider 所包含的 component 都可以取得 Content。

`子層 component 的部分：`

呼叫 `useContext` 的 component 會在 context 值更新時重新 render，以前是要用 Context Consumer 來取值，有了 React Hooks 後子組件就可以利用`useContext`來取得資料

```javascript
const content = useContext(Context);
```

`層級關係＝> ContentExample > SideBar > SideBarButton（爺、父、孫）`

在爺爺身上注入資料，孫子用 useContext 不透過父親也能拿到爺爺的 value

![](/img/1__ApHCRRPUZtIzJ__kiAkR9kQ.png)

此時可能會冒出一個疑問，明明我在 createContext 的已經傳入了預設值 ，為什麼在 Context Provider 的 value 還要再傳一遍？我也是寫完才發現這件事，Context Provider 的 value 為可傳也可不傳 ，但如果傳值就會以傳入的 value 為優先，而非預設值。

好，寫到這裡時剛好我看到一段關於 useContext 的教學影片，發現其實 context.provider 可以不用寫，心裡想說怎麼可能！因為我查到的所有範例都有寫 context.provider，試著把 context.provider 拔掉居然還是可以正常運作?

> 教學影片的結論是使用 useContext 時將不再需要 Provider&Consumer，不過這部分目前找不到相關資料佐證，先持保留態度好了

假設子 component 和父 component 不在同一個 component 裡面，如下圖

![](/img/1__8Es180mGBOQNBlSrWjBg9w.png)

子 component 就可以透過 props 來取得 context

![](/img/1__7io9JIX3UfMQsuUkKWm75A.png)

但這樣感覺還是沒有很方便，有其他的方法嗎？有的！把 content export 出來 再引入即可

```javascript
// ContextExample.js 將 Content export

export const Content = createContext(slogan);
```

ContextExample2.js import Content

![](/img/1__beCl9VEpJHwPzu__svUiP0A.png)

但這樣寫，當 createContext 數量變多，就會變得有點混亂，所以建議新增一個 store，裡面就專門存放 createContext，方便管理

### useReducer

再次回想到之前寫 React-Redux 被搞得暈頭轉向，要引入 Redux、React-Redux，然後在寫對應的設定，對於一個 React 新手來說就像迷宮一樣，有種我現在到底在哪裡的錯覺， 還好現在有 useReducer 可以簡化這個流程

```javascript
const [count, dispatch] = useReducer(reducer, initialState, initialAction);
```

- **reducer :** 跟以往的 reducer 一樣， 先列出有哪些動作指令 ，並且根據不同的動作回傳操作過後的 state
- **initialState:**定義初始值
- **initialAction:**為 useReducer 初次執行的 action，但貌似不常用

將 reducer 和 initialState 傳入 userReducer 並且以解構的方式回傳

第一個值為 state，第二個值為 dispatch

接著只要透過 dispatch 就可以成功修改值，下面以一個簡單的計數器為例子，可以看得出來流程簡化了不少

![](/img/1__STwAxH3PEdtSrDm4AqqhOg.png)

跟之前的 React-Redux 比起來，這個寫法真的親民的許多呀！
