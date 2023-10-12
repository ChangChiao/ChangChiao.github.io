---
title: 如何在vue輕鬆使用svg? —  svg-sprite-loader
description: >-
  在開發網站的時候，如果需要用到icon的話我們通常會使用Font
  Awesome這類的工具來滿足我們的需求，但更多時候我們需要使用客制化的icon，因此UIUX設計師會提供svg的檔案給前端工程師來做使用，那麼此時會有幾種方式來使用這些svg
date: '2022-10-25T01:59:10.719Z'
categories: []
keywords: []
slug: >-
  /@joe-chang/%E5%A6%82%E4%BD%95%E5%9C%A8vue%E8%BC%95%E9%AC%86%E4%BD%BF%E7%94%A8svg-svg-sprite-loader-28a0d4a5c645
---

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__XZRV__clX2gUqNkWozl5MHw.jpeg)

在開發網站的時候，如果需要用到icon的話我們通常會使用Font Awesome這類的工具來滿足我們的需求，但更多時候我們需要使用客制化的icon，因此UIUX設計師會提供svg的檔案給前端工程師來做使用，那麼此時會有幾種方式來使用這些svg

*   把svg轉換成font字型檔引用

我們可以將svg上傳到icomoon 、iconfont這類的網站幫我們生成字型檔之後，再寫一些設定就可以將svg拿來當作font使用，缺點是維護較為麻煩，每次icon有新增修改都必須先上傳svg到網站上再下載字型檔，而且近期iconfont蠻不穩定的，很常不能上傳，造成許多開發者的困擾

*   以inline的方式使用svg

這個方式最簡單，直接複製svg的path然後一股腦的貼上html即可，也可自行修改icon的顏色，但可讀性實在是非常的差，所以基本上應該沒有人會使用這個方法

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__oWIo4ehgUgErm4RPQH9AFQ.png)

*   把svg直接當img引入

無法修改svg的顏色，使用上比較不彈性

*   把svg當作component使用

理想的狀況是我們可以將svg當作元件使用，只要帶入svg的名稱就能夠使用指定的svg，此時就是svg-sprite-loader登場的時候了! svg-sprite-loader的原理是將svg一個一個放進symbol拼成雪碧圖，如下圖

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__ieqVj__OY83uaUy__vSsyzhA.png)

需要用到指定的svg時就用use標籤引用，並且將連結設為svg的id，指定的svg就會顯示在畫面上

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__5yOaRxSg4rTEc2aElADDuQ.png)

對於 svg-sprite-loader有了基本概念之後，就可以來實作了，這次使用vue3 + vue cli開發

#### svg-sprite-loader 的設定步驟

先安裝 svg-sprite-loader

`yarn add svg-sprite-loader -D`

**vue.config.js設定**

由於src/assets/icons底下的svg要作成雪碧圖，因此webpack的svg loader要排除該資料夾，否則會無法生成svg雪碧圖

每當要使用icon的時候，都必須手動引入對應的svg，實在是很麻煩，因此我們可以在main.js使用webpack的require.context方法，一口氣引用所有的svg檔案，一勞永逸!

**icon 元件**

為了方便使用，接下來建立一個icon component，props帶入svg的檔名就可以使用對應的svg，也能夠帶入尺寸，控制icon的大小

> 記得加上fill currentColor，svg才會繼承父層容器所設定的顏色，如果改不動顏色應該是svg本身有設定顏色，記得請設計師去掉

假設每次要使用icon元件都需要寫一次引用就會變得有點繁瑣，因此我們可以將Icon元件設為全域的元件

最後就可以隨心所欲的使用icon 元件了!

![](/Users/joectchang_mac/Downloads/medium-export-a/post2022/md_1697073583233/img/1__9wnT6TmkxoNiY4hVdo6Qrw.png)