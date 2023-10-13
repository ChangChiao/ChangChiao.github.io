---
title: React hook(中)-useContext&useReducer
description: useContext()
date: '2020-11-16T15:55:18.489Z'
categories: []
keywords: []
slug: /@joe-chang/react-hook-%E4%B8%AD-usecontext-usereducer-1a0efe78f0a5
---

![](/Users/joectchang_mac/Downloads/medium-export-a/fail/md_1697158079610/img/1__au40ZgVt3x6DrLVe__ptQ9w.jpeg)

useContext會和React Context API搭配使用，可以讓component共享資料，像是進階版的props，不用一層一層的傳props真的太美好了，可以隔山打牛，可以避免掉HOC的波動拳…不得不說這樣真的方便多了。

#### React Context API

父層component的部分：

利用createContext建立 Context component，createContext 可傳入預設值，Context Provider需要包住整個組件，Context Provider的value記得要傳入需要傳遞的值，只要是被provider所包含的component都可以取得Content。

子層component的部分：

呼叫 `useContext` 的 component 會在 context 值更新時重新 render，以前是要用Context Consumer來取值，有了React Hooks後子組件就可以利用`useContext`來取得資料

const content = useContext(Context)

層級關係＝> ContentExample > SideBar > SideBarButton（爺、父、孫）

在爺爺身上注入資料，孫子用useContext不透過父親也能拿到爺爺的value

![](/Users/joectchang_mac/Downloads/medium-export-a/fail/md_1697158079610/img/1__ApHCRRPUZtIzJ__kiAkR9kQ.png)

此時可能會冒出一個疑問，明明我在createContext的已經傳入了預設值 ，為什麼在Context Provider的value還要再傳一遍？我也是寫完才發現這件事，Context Provider的value為可傳也可不傳 ，但如果傳值就會以傳入的value為優先，而非預設值。

好，寫到這裡時剛好我看到一段關於useContext的教學影片，發現其實context.provider可以不用寫，心裡想說怎麼可能！因為我查到的所有範例都有寫context.provider，試著把context.provider拔掉居然還是可以正常運作?

> 教學影片的結論是使用useContext時將不再需要Provider&Consumer，不過這部分目前找不到相關資料佐證，先持保留態度好了

假設子component和父component不在同一個component裡面，如下圖

![](/Users/joectchang_mac/Downloads/medium-export-a/fail/md_1697158079610/img/1__8Es180mGBOQNBlSrWjBg9w.png)

子component就可以透過props來取得context

![](/Users/joectchang_mac/Downloads/medium-export-a/fail/md_1697158079610/img/1__7io9JIX3UfMQsuUkKWm75A.png)

但這樣感覺還是沒有很方便，有其他的方法嗎？有的！把content export出來 再引入即可

_ContextExample.js_ 將Content export

export const Content = createContext(slogan)

_ContextExample2.js_ import Content

![](/Users/joectchang_mac/Downloads/medium-export-a/fail/md_1697158079610/img/1__beCl9VEpJHwPzu__svUiP0A.png)

但這樣寫，當createContext數量變多，就會變得有點混亂，所以建議新增一個store，裡面就專門存放createContext，方便管理

### useReducer

再次回想到之前寫React-Redux被搞得暈頭轉向，要引入Redux、React-Redux，然後在寫對應的設定，對於一個React新手來說就像迷宮一樣，有種我現在到底在哪裡的錯覺， 還好現在有useReducer可以簡化這個流程

const \[count, dispatch\] = useReducer(reducer, initialState, initialAction)

*   **reducer :** 跟以往的reducer一樣， 先列出有哪些動作指令 ，並且根據不同的動作回傳操作過後的state
*   **initialState:**定義初始值
*   **initialAction:**為useReducer初次執行的action，但貌似不常用

將reducer和initialState傳入userReducer 並且以解構的方式回傳

第一個值為state，第二個值為dispatch

接著只要透過dispatch就可以成功修改值，下面以一個簡單的計數器為例子，可以看得出來流程簡化了不少

![](/Users/joectchang_mac/Downloads/medium-export-a/fail/md_1697158079610/img/1__STwAxH3PEdtSrDm4AqqhOg.png)

跟之前的React-Redux比起來，這個寫法真的親民的許多呀！