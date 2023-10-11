---
title: 用Redux Thunk 來處理非同步action
description: >-
  Redux
  Thunk的目的就是為了實踐middleware，讓action能做更多的事，想必大家都曾經看過這個錯誤，當action為非同步的時候，就會報錯，Redux
  Thunk就是為了解決這個問題而生
date: "2021-01-04T15:01:58.520Z"
categories: []
keywords: []
tag: react
---

![](/img/1__gf4tBJx__PdjmBLS4rRartA.jpeg)

Redux Thunk 的目的就是為了實踐 middleware，讓 action 能做更多的事，想必大家都曾經看過這個錯誤，當 action 為非同步的時候，就會報錯，Redux Thunk 就是為了解決這個問題而生

![](/img/1__ekcM1PkJ3fMjDpeOWxRWJw.png)

來看一下 Redux Thunk 的原始碼

```javascript
function createThunkMiddleware(extraArgument) {
  return ({ dispatch, getState }) =>
    (next) =>
    (action) => {
      if (typeof action === "function") {
        return action(dispatch, getState, extraArgument);
      }

      return next(action);
    };
}

const thunk = createThunkMiddleware();
thunk.withExtraArgument = createThunkMiddleware;

export default thunk;
```

你沒看錯，就只有這樣。

原本的 action creator 都是回傳 action object，在 Redux Thunk 裡面會檢查若 action 不是 object 而是 function，就會執行 function 後再將 action 往下傳，直到回傳 object 才會進去 reducers

那麼接著來實作一下，情境很簡單，call api 拿到資料後 dispatch 更新資料，讓畫面依據資料長出列表

記得要先安裝

```bash

npm install redux-thunk --save
```

store 的部分因為比之前複雜，就從 index.js 抽離出來獨立

createStore 可以接受三個參數（reducer、初始 state、applyMiddleware），而 applyMiddleware 又可以傳入三個參數（thunk、promise、logger

![](/img/1__K2LLVncApmUK0TkWlvy8rA.png)

順帶一提，我有另外安裝 redux-logger，會在 action 觸發和 state 變化時自動印出 log，個人覺得在除錯方面還蠻方便的

![](/img/1__stLLJs2Q2K2TpjEknQlZPA.png)

index.js 將 store 傳入

![](/img/1__ZarBppUuBsvlbtDu4YJceA.png)

這邊為了簡單做個練習，所以只寫了一個 action，處理資料接收，fetchPosts 在拿到 api 資料後 dispatch receivePosts(json.data)，可以發現 fetchPosts 是回傳一個函式

> getBikeList 可以同時拿到 getState 和 dispatch 方法

![](/img/1__6N2M6dT60CsF2YQ1BtrbvA.png)

reducersBike.js

經過 Middleware 處理後，最後將資料傳給 reducer，改變 state 之後重新渲染 UI 畫面

![](/img/1__y5__qlwXaC__UniuzdhkEMiQ.png)

一切準備就緒之後，在 componentDidMount 階段 dispatch getBikeList，在還沒拿到資料等待 api 回應之前先顯示 loading，（本來是要接高雄 Youbike 據點的 api，但 api 時好時壞，只好改接市場資料 orz

![](/img/1__jqO3AQHfT07Ep__U7965wJg.png)

下面這部分就跟以往的 redux 一樣

![](/img/1__BPEhOFjLStr2AGsnibEE4w.png)

最後成功拿到列表！

![](/img/1__iIFAZUuqTTv251XRrtqr__Q.png)

除了 react-thunk 之外，再處理 Middleware 非同步這塊，Redux Saga 也會是個不錯的選擇，日後有機會再來分析兩者的差異。
