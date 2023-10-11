---
title: 如何用vue cli 搭配Tailwind CSS開發
description: >-
  最近在技術社群和實體技術分享會都會聽到Tailwind的名字 ，想說這到底是什麼東西 ？討論熱度這麼高？稍微google了一下原來是CSS
  Framework？但就我以前對CSS…
date: "2021-03-29T01:16:51.917Z"
tag: vue
categories: vue
keywords: []
---

![](/img/1__be__1YYiD8cGVJZu81iP7QQ.jpeg)

最近在技術社群和實體技術分享會都會聽到 Tailwind 的名字 ，想說這到底是什麼東西 ？討論熱度這麼高？稍微 google 了一下原來是 CSS Framework？但就我以前對 CSS Framework 的印象不是都很肥大嗎？如果想要客製化樣式也也不是那麼方便（使出!important 大法），所以只有前期維護舊專案會用到，後期幾乎都是自己手刻樣式。

不過在看完 Tailwind 的官網之後，真的讓我大開眼界，雖然 Tailwind 本體比 Bootstrap 還要肥大，但….居然可以把沒用掉的 css 全部刪掉，打包出來的 CSS 檔案大小不過幾 10KB，對於這輩子看過 1MB 多的 CSS 我來說，這簡直就是一個夢幻的數字！完美解決的 CSS 框架最為人詬病的肥大問題。

> Bootstrap、Material-UI、Bulma 都是 UI Framework，提供了現成的元件樣式供開發者套用，但需要客製化的話就要覆寫掉原本的樣式，而 Tailwind 則是 Utility Framework(通用類別)，可以根據自身需求或是不同用途去客製化。

#### 前置作業

建立好 vue 專案之後 ，只要執行 vue add tailwind 就會自動幫你安裝 Tailwind 並且產生設定檔，這邊會詢問你要建立哪種版本的 tailwind config js

![](/img/1__kk32aFZel1mcZpHQ19181Q.png)

選 none 就不會產出 tailwind.config.js 了，因為想要自己定義變數， 所以先選擇 minimal，如果選擇 full 版本 ，則會建立一個完整的設定檔、包含顏色定義、RWD 定義、間隔定義等等鉅細彌遺地列出來，詳細的設定檔內容可以看[這裡](https://github.com/tailwindlabs/tailwindcss/blob/master/stubs/defaultConfig.stub.js)。

![](/img/1____zVD6OyY0N6xi4SasDzSsw.png)

除了生成 tailwind.config 以外，會發現根目錄多了一個 postcss.config.js

![](/img/1__W__E3ZY1N07SI4a3XoxVOLw.png)

看到這裡才發現，原來 Tailwind 是 PostCSS 的套件之一！（官網明明就有寫 🤣

一直以來提到 PostCSS 就會想到 autoprefixer，但直到今天才知道除了能夠自動添加瀏覽器前綴之外，PostCSS 還提供了很多套件可以使用

- cssnext —  可以使用最新的 css 語法
- styleLint —  檢查 css 語法是否有錯誤
- cssnano —  壓縮 css

同時也會發現在 assets 底下多了一個 tailwind.css 檔案，自動幫我們引入 base(_reset.css_)、components (_元件樣式_)、utilities(_共用樣式_)

![](/img/1__zz7k__QGwn4oJqnwuDpEqQA.png)

在 main.js 引入 tailwind.css (其實透過執行 vue add tailwind，已經自動幫我們引入好了)

```javascript
import "./assets/tailwind.css";
```

#### 開始寫樣式吧

```html
<div class="items-center justify-center font-bold text-green-500 text-7xl">
  hello
</div>
```

Tailwind 的 class 具有語意化的設計，雖然很快就能上手，不過這樣一坨拉庫的 class 不是很好閱讀，如果又有很多地方要用到一樣的組合，就會不好維護，難道沒辦法把這些 class 整合在一起嗎？答案是有的，我們可以自己定義 class，再將需要的樣式@apply 進來，就可以實現自定義元件。

但這時會發現第二個 class 會出現紅色波浪符號，因為 VSCode 認定這是不合法寫法，有兩種方式可以解決這個問題

![](/img/1__rrz69PqCu0ByQwsYPIDSeg.png)

1. 只要將 lang 改成 postcss 就可以了

![](/img/1__pbObFyC5il9EuFgvD6Z4__Q.png)

2. 安裝 VSCode 擴充套件 styleLint，將 tailwind 排除在檢查的範圍之外，詳細設定請參考這篇文章 [Visual Studio Code CSS linting with Tailwind](https://www.meidev.co/blog/visual-studio-code-css-linting-with-tailwind/)

正當我覺得一切都準備就緒，執行 yarn serve 的時候卻報了 PostCSS 的 error！讓人一頭霧水，明明我的專案就有安裝 PostCSS，只好在 Tailwind 的官網找一下有沒有對應的說明，然後看到了這行字

> _Tailwind CSS requires Node.js 12.13.0 or higher._

立刻用 nvm 安裝 12.13.x 的*Node*穩定版本，就可以成功運行了！

![](/img/1__e4m1G7RjghZNsekfcj1omA.png)

#### 搭配 VSCode 擴充套件開發

知道關鍵字，但想不起來完整的 class 名稱怎麼辦？沒關係！馬上安裝 VSCode 擴充套件 [tailwindcss-intellisense](https://github.com/tailwindlabs/tailwindcss-intellisense) ，只要輸入前幾個字，就會跳出對應的提示字，加速開發效率。

![](/img/1__tqzpUxoigQWtkc7YVndX6A.png)

安裝完畢後，還需要調整 VSCode 一些[相關設定](https://github.com/tailwindlabs/tailwindcss-intellisense)才會生效

1.  根目錄一定要有 tailwind.config.js，擴充套件才會有反應
2.  開啟 emmet Completioms （in VSCode setting)

![](/img/1__HM6VzEdqPB8q7ZGVyC3C__A.png)

3.因為 tailwind 用了很多 css 看不懂的語法，為了防止出現大量的錯誤提示，要先將 css.validate 改為 false（in VSCode setting)

這時可能會發現一個問題，在一般的 html 上編輯 class 確實會出現提示字，但是在 vue template 裡面卻是一點反應都沒有，這時就要在 tailwindCSS.includeLanguages 補上對應的設定，可參考[tailwindcss-intellisense](https://github.com/tailwindlabs/tailwindcss-intellisense) GitHub issues 的[這篇](https://github.com/tailwindlabs/tailwindcss-intellisense/issues/49)討論

![](/img/1__xaesRmKtTy6eAHWv9OHqLQ.png)

重新開啟 VSCode 之後，撰寫 class 時就會出現提示字了 🎉（好不容易

![](/img/1__wMocYOQ5__ZaoK1N__fZ9k0A.png)

總結一下，今天快速的試玩了一會 Tailwind，可以理解為什麼近期這個 CSS Framework 這麼熱門了，光是擁有高度客製化的彈性和自動刪除用不到的樣式這兩大優點就能吸引不少人入坑了，雖然初始化設定的流程稍微複雜了一點（今天是因為用了[vue-cli-plugin-tailwind](https://www.npmjs.com/package/vue-cli-plugin-tailwind)，才省下不少步驟），但官網的文件寫得非常清楚，幾乎什麼問題都可以在上面找到解答，算是非常好上手，搭配任何框架也都沒問題，假如不用管古董級瀏覽器 IE 9 的話，真的非常推薦用 Tailwind 來開發！👏
