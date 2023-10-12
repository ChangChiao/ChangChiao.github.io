---
title: 如何在vue輕鬆使用svg? —  svg-sprite-loader
description: >-
  在開發網站的時候，如果需要用到icon的話我們通常會使用Font
  Awesome這類的工具來滿足我們的需求，但更多時候我們需要使用客制化的icon，因此UIUX設計師會提供svg的檔案給前端工程師來做使用，那麼此時會有幾種方式來使用這些svg
date: "2022-10-25T01:59:10.719Z"
categories: []
keywords: []
slug: >-
  /@joe-chang/%E5%A6%82%E4%BD%95%E5%9C%A8vue%E8%BC%95%E9%AC%86%E4%BD%BF%E7%94%A8svg-svg-sprite-loader-28a0d4a5c645
---

![](/img/1__XZRV__clX2gUqNkWozl5MHw.jpeg)

在開發網站的時候，如果需要用到 icon 的話我們通常會使用 Font Awesome 這類的工具來滿足我們的需求，但更多時候我們需要使用客制化的 icon，因此 UIUX 設計師會提供 svg 的檔案給前端工程師來做使用，那麼此時會有幾種方式來使用這些 svg

- 把 svg 轉換成 font 字型檔引用

我們可以將 svg 上傳到 icomoon 、iconfont 這類的網站幫我們生成字型檔之後，再寫一些設定就可以將 svg 拿來當作 font 使用，缺點是維護較為麻煩，每次 icon 有新增修改都必須先上傳 svg 到網站上再下載字型檔，而且近期 iconfont 蠻不穩定的，很常不能上傳，造成許多開發者的困擾

- 以 inline 的方式使用 svg

這個方式最簡單，直接複製 svg 的 path 然後一股腦的貼上 html 即可，也可自行修改 icon 的顏色，但可讀性實在是非常的差，所以基本上應該沒有人會使用這個方法

![](/img/1__oWIo4ehgUgErm4RPQH9AFQ.png)

- 把 svg 直接當 img 引入

無法修改 svg 的顏色，使用上比較不彈性

- 把 svg 當作 component 使用

理想的狀況是我們可以將 svg 當作元件使用，只要帶入 svg 的名稱就能夠使用指定的 svg，此時就是 svg-sprite-loader 登場的時候了! svg-sprite-loader 的原理是將 svg 一個一個放進 symbol 拼成雪碧圖，如下圖

![](/img/1__ieqVj__OY83uaUy__vSsyzhA.png)

需要用到指定的 svg 時就用 use 標籤引用，並且將連結設為 svg 的 id，指定的 svg 就會顯示在畫面上

![](/img/1__5yOaRxSg4rTEc2aElADDuQ.png)

對於 svg-sprite-loader 有了基本概念之後，就可以來實作了，這次使用 vue3 + vue cli 開發

#### svg-sprite-loader 的設定步驟

先安裝 svg-sprite-loader

`yarn add svg-sprite-loader -D`

**vue.config.js 設定**

由於 src/assets/icons 底下的 svg 要作成雪碧圖，因此 webpack 的 svg loader 要排除該資料夾，否則會無法生成 svg 雪碧圖

每當要使用 icon 的時候，都必須手動引入對應的 svg，實在是很麻煩，因此我們可以在 main.js 使用 webpack 的 require.context 方法，一口氣引用所有的 svg 檔案，一勞永逸!

**icon 元件**

為了方便使用，接下來建立一個 icon component，props 帶入 svg 的檔名就可以使用對應的 svg，也能夠帶入尺寸，控制 icon 的大小

> 記得加上 fill currentColor，svg 才會繼承父層容器所設定的顏色，如果改不動顏色應該是 svg 本身有設定顏色，記得請設計師去掉

假設每次要使用 icon 元件都需要寫一次引用就會變得有點繁瑣，因此我們可以將 Icon 元件設為全域的元件

最後就可以隨心所欲的使用 icon 元件了!

![](/img/1__9wnT6TmkxoNiY4hVdo6Qrw.png)
