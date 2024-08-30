---
title: 用cypress來寫E2E Test吧!
description: >-
  當網站開發完之後，我們習慣到處點點看，跑一次使用者的操作行為，檢查頁面的功能是否正常，但很多時候我們的手動測試都是憑感覺，就像戳戳樂一樣，沒有固定的流程和檢查的項目，有戳到bug就是撿到了，沒戳到就當作相安無事，所以難免會有漏網之魚，因此我們需要有一套工具來輔助我們來做有條理測試…
date: "2022-03-03T03:48:17.408Z"
categories: e2eTest
tag: cypress
keywords: []
---

![](/img/1__j7ih2zzcCyifrZJ2lOy0FQ.png)

當網站開發完之後，我們習慣到處點點看，跑一次使用者的操作行為，檢查頁面的功能是否正常，但很多時候我們的手動測試都是憑感覺，就像戳戳樂一樣，沒有固定的流程和檢查的項目，有戳到 bug 就是撿到了，沒戳到就當作相安無事，所以難免會有漏網之魚，因此我們需要有一套工具來輔助我們來做有條理測試，就是 cypress! cypress 會依照我們寫的腳本，幫我們模擬測試行為，cypress 採用了 mocha 的語法，聽說非常的好上手，還可以搭配 GUI(圖形介面)操作，那就來試試看 cypress 是否真的這麼好用吧!

**甚麼是 E2E Test?**

> 所謂的「端對端」(**End-to-end testing**) 是指從使用者的角度出發(一端)，對真實系統(另一端)進行測試。 (引用自[保哥部落格](https://blog.miniasp.com/))

#### 安裝 Cypress

**npm**

```bash
npm install cypress --save-dev
```

**yarn**

```bash
yarn add cypress --dev
```

安裝 cypress 之後，可以直接執行 npx cypress open，或是在 package.json 新增指令，看個人習慣

```json
"test:e2e": "cypress open"
```

執行指令之後除了打開一個 cypress 視窗之外，也會在專案底下自動新增 cypress 相關的檔案

![](/img/1__ftgoiLnqIeepVOfxLolCYA.png)

打開 cypress 視窗，會發現 cypress/integration 資料夾底下已經放了一些範例用的測試程式碼，可以隨便點一個進去看看

![](/img/1__CZwwS6FozeCZF__pBb9ijDg.png)

點進去後就會自動執行腳本，左邊的區塊會顯示測試流程並且依序的執行，右邊的畫面則會跟著測試步驟跑一次

![](/img/1__rrrROOvQCUdv4OneT8AsDQ.gif)

#### 來寫一個測試吧!

在 cypress/integration 底下新增`login_spec.js`，檔案的名稱可以依照自己要測試的功能命名，觀察範例的命名格式為`xxx_spec.js`

![](/img/1__D77MIF__WumCHj9xqZSOCdg.png)

因為登入的介面比較單純，輸入帳號密碼按下登入就結束了，所以很適合新手拿來練習 cypress

![](/img/1__xyPcL8gF2nEcqo3Q6eXj__w.png)

[**get | Cypress Documentation**  
\_Get one or more DOM elements by selector or alias. The querying behavior of this command is similar to how works in…\_docs.cypress.io](https://docs.cypress.io/api/commands/get "https://docs.cypress.io/api/commands/get")[](https://docs.cypress.io/api/commands/get)

這邊必須讚嘆一下 cypress 的文件寫得很清楚，提供了大量的範例還有教學影片，快速瀏覽一次就會對 cypress 的寫法有個頭緒，在讀完文件後寫了一個簡易的登入測試流程，接下來會針對幾個關鍵字來作說明

#### 測試語法

- describe: 測試群組，裡面可以寫多個測試
- beforeEach: 測試開始前要做的事情
- visit : 開啟要測試的網址
- get : 獲取目標 DOM 元素，選取器的方式類似 jQuery
- type : 在 DOM 元素輸入內容
- click : 執行點擊動作
- it : 撰寫測試案例(test case)
- should: 斷言，預期的執行結果應該要是甚麼

寫好之後回到 cypress 的視窗，點擊剛剛新增的 login_spec.js 就會自動執行

![](/img/1__7Vbm3VAc5Qr06__LOgXjmmQ.png)

因為是第一次使用所以一不小心就寫錯語法或是犯了一些很白癡的錯誤，不過 cypress 除了報錯之外，還會很貼心的把錯誤的內容解釋得很清楚，因此可以很快的修正錯誤

![](/img/1__va65Z1Muv0p8LQ2vpp1NQA.png)

上面的錯誤原因是我用 button 標籤來選取登入按鈕(偷懶)，但我的頁面上其實有兩個 button 元素，因此 get("button")會選到兩個元素，而 click 函式只能針對一個元素執行，所以就報錯了，cypress 建議如果我們是想要針對多個 button 綁定 click 函式的話，可以傳入 { multiple : true}，不過這邊我只想要針對一個按鈕寫 click 事件，所以調整一下選取器的寫法來修正這個問題

```javascript
//錯誤的
cy.get("button").click();

//正確的
cy.get("button[type='submit']").click();
```

test case 要檢核內容很簡單，只是驗證密碼是否等於 123456 而已，如果刻意將輸入的密碼改成 1234568，cypress 就會顯示錯誤

![](/img/1____xOYa11X0cUMGhzusCnM7Q.png)

如果正確填入 123456，就會通過測試

![](/img/1__F2N0y5QuGrx6dRv97QrS8Q.png)

看看這個測試執行速度，手動測試完全看不到 cypress 的車尾燈阿!

![](/img/1__JVKvhcu2dNDC__oP33__asog.gif)

在還沒看清楚發生甚麼事之前，測試腳本已經跑完了，如果想要仔細看每個步驟的測試行為該怎麼辦呢  ? Time Travel 的功能很方便，將游標滑到每個測試步驟上，就像慢動作回放一樣，可以看清楚每個測試步驟做了甚麼操作

![](/img/1__iJzZaEmT0bjQ7AElaxI3ug.gif)

以上就是關於 cypress 的簡單介紹，其實還有蠻多功能沒有講到的，就讓大家自己去探索了，寫測試的過程還蠻有趣的，不知道甚麼時候有機會在工作的專案上寫測試呢  ? 感覺是遙遙無期阿… (看著緊湊的專案工作時程表)
