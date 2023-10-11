---
title: 用React 來做一個todolist吧！
description: 不管是學哪一種框架，todolist可以說是經典練習題，此次練習除了實現基本的功能新增與刪除之外，再加一個拖曳改變排序功能
date: "2021-01-11T06:20:04.954Z"
categories: react
keywords: []
tag: react
---

![](/img/1__Sh4Q5BP0D6DpSKggj2tmWQ.jpeg)

不管是學哪一種框架，todolist 可以說是經典練習題，此次練習除了實現基本的功能新增與刪除之外，再加一個拖曳改變排序功能

- 新增 ：當使用者輸入完要新增的代辦項目後並按下 enter 後 ，就會觸發新增的 function ，並且檢查輸入值是否為空，若為無效的值會跳 alert 提醒使用者
- 刪除  : 按下刪除時 ，參數帶入該項目的 index ，並利用 splice 移除
- 拖曳排序  : 拖曳排序功能利用 HTML5 Drag & Drop API 來實現，在拖曳起始的時候會先將拖曳對象的 index 存在 BeginIndex，在滑過其他項目時觸發 dispatch ，調整當前排序，當拖曳結束後清除 BeginIndex

雖然用到 redux 來處理 todolist 有點殺雞焉用牛刀，但就是拿來練習看看!

本來以為很快就可以搞定一切，殊不知遇到一個很神秘的問題， 我刪除了第一個項目，但居然一口氣被刪了兩個，其實不只是刪除 ，連新增也是會執行兩次，查看觸發的 function 只有執行一遍，但是 reducer 的部分居然執行了兩次？為什麼？

透過估狗查到的解法是，將 reducer 獨立出來，不要放在原本的 function component 裡面，因為 reducer 的 instance 會隨著 function component 變化，將 reducer 抽離出來之後，恩..完全沒有反應，reducer 還是觸發兩遍

最後的解決方法是移除 React.StrictMode，如果用了 StrictMode 嚴格模式，那麼 redux 就會被多次的觸發，確保運作正常

> There is no “problem”. React intentionally calls your reducer twice to make any unexpected side effects more apparent. Since your reducer is pure, calling it twice doesn’t affect the logic of your application. So you shouldn’t worry about this.

> In production, it will only be called once.

不過我不確定這是否為正規的做法，但為了讓程式可以正常運作，只能先摘掉 StrictMode，如果有更好的做法歡迎告知

![](/img/1__RBcXVzMCOwqM3rtrtRfcOQ.png)

完整程式碼如下

```javascript
import React, { useState, Fragment, useReducer } from "react";
const initalState = {
  list: ["shopping", "sleep"],
};
const reducer = (state, action) => {
  console.log("action", action);
  switch (action.type) {
    case "ADD_ITEM": {
      return {
        list: action.oldList,
      };
    }
    case "DELETE_ITEM": {
      state.list.splice(action.index, 1);
      console.log("oldold", state.list);
      return {
        list: state.list,
      };
    }
    case "EXCHANGE_ITEM": {
      const fromIndex = action.fromIndex;
      const toIndex = action.toIndex;
      console.log(action.fromIndex, action.toIndex);
      console.log("before", state.list);
      const [del] = state.list.splice(fromIndex, 1);
      state.list.splice(toIndex, 0, del);
      console.log("after", state.list);
      return {
        list: state.list,
      };
    }
    default: {
      return state;
    }
  }
};
const useListReducer = () => {
  return useReducer(reducer, initalState);
};
const ToDoList = () => {
  const [state, dispatch] = useListReducer();
  const [inputValue, setInputValue] = useState("");
  const [BeginIndex, setaBeginIndex] = useState(0);
  const deleteItem = (index) => {
    console.log("deleteIndex", index);
    dispatch({
      type: "DELETE_ITEM",
      index,
    });
  };
  const addItem = (e) => {
    const value = e.target.value;
    if (e.key === "Enter") {
      if (!value) alert("請輸入代辦項目");
      let oldList = [...state.list];
      oldList.push(value);
      dispatch({
        type: "ADD_ITEM",
        oldList,
      });
      setInputValue("");
    }
  };
  // const [state, dispatch] = useReducer(reducer, initalState);
  const handleChange = (e) => {
    setInputValue(e.target.value);
  };
  return (
    <Fragment>
      <h3> 新增代辦項目 </h3>{" "}
      <input value={inputValue} onChange={handleChange} onKeyDown={addItem} />{" "}
      <ul>
        {" "}
        {state.list.map((item, i) => {
          return (
            <ol
              draggable="true"
              onDragStart={() => {
                setaBeginIndex(i);
              }}
              onDragEnter={() => {
                dispatch({
                  type: "EXCHANGE_ITEM",
                  fromIndex: BeginIndex,
                  toIndex: i,
                });
                setaBeginIndex(i);
              }}
              onDragEnd={() => {
                setaBeginIndex(undefined);
              }}
              key={i}
            >
              <span> {item} </span>{" "}
              <button
                onClick={() => {
                  deleteItem(i);
                }}
              >
                {" "}
                delete{" "}
              </button>{" "}
            </ol>
          );
        })}{" "}
      </ul>{" "}
    </Fragment>
  );
};
export default ToDoList;
```

參考資料

[**useReducer Action dispatched twice**  
\_To first clarify the existing behavior, the STATUS_FETCHING action was actually only being "dispatched" (i.e. if you do…\_stackoverflow.co](https://stackoverflow.com/questions/54892403/usereducer-action-dispatched-twice "https://stackoverflow.com/questions/54892403/usereducer-action-dispatched-twice")[](https://stackoverflow.com/questions/54892403/usereducer-action-dispatched-twice)
