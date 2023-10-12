---
title: E2E test的明日之星 — playwright
description: >-
  說到E2E(End to
  End)測試工具，大部分的人應該第一個想到的會是Cypress，不過其實除了Cypress，還有一個好用的工具，那就是由微軟開發的後起之秀 — playwright，近期因為工作需求需要寫E2E…
date: '2023-07-22T10:52:17.110Z'
categories: []
keywords: []
slug: >-
  /@joe-chang/e2e-test%E7%9A%84%E6%98%8E%E6%97%A5%E4%B9%8B%E6%98%9F-playwright-de85659b56b9
---

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__no__d95uHh8__Vu67iGJ52ug.jpeg)

說到E2E(End to End)測試工具，大部分的人應該第一個想到的會是Cypress，不過其實除了Cypress，還有一個好用的工具，那就是由微軟開發的後起之秀 — playwright，近期因為工作需求需要寫E2E test，便將cypress和playwright列入考量清單，雖然cypress歷史較為悠久但因為跑ci時耗費的資源較多，幾經評估之後我們團隊決定選擇playwright，跟著官方的文件玩過一次，發現還蠻有趣的，接下來會介紹playwright有哪些特點

### Trace viewer — 動作錄製

在網頁上的所有操作行為， playwright都能幫你錄製，當你按下錄製按鈕之後playwright會將你在網頁上的每一個操作步驟都寫成程式碼(如下圖)，完全不用自己動手

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__pytjxluVwr47OIk8STlrZw.png)

#### ui介面操作

playwright提供了ui介面方便使用者查看每個測試階段，左邊會列出測試步驟，上方則會有逐格的畫面錄製，可以觀察每個畫面的變化，下方的面板有console、network等等的資訊，讓開發者更容易除錯

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__LIgaw3PBp6tBD76M68MTig.png)

#### 測試報告

會依照test case和瀏覽器逐一生成測試報告，讓測試結果一目瞭然，後方也會列出執行秒數

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__ssnE0aA6z__NhGg__WR__JRdg.png)

點開標題，內頁會明確地告訴你測試的細節，哪個測試環節沒有pass等等

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__QzSjmFdSGwda6CrHaySMDA.png)

#### 小試身手

環境配置：pnpm + svelte + mock server + playwright

在開始寫測試之前，強烈推薦大家安裝vscode playwright套件，因為這個工具真的非常方便，後面會再做介紹

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__ZTaDMLChrhhYj6XWeNX5lw.png)

#### 如何開始

在一個既有的專案根目錄底下執行指令，使用npm和yarn的朋友請看[官網的指令](https://playwright.dev/docs/intro)，和pnpm的有些許差異

pnpm dlx create-playwright

接下來會詢問你一些問題，可根據自身需求做調整

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__qTkJv1e2YWtUK0QtHA2rYw.png)

不想用command line的話，也可以用另一個方式安裝 playwright

假如先前已經安裝好playwright vscode套件，可以按ctrl(cmd) + shift + p，呼叫執行指令的輸入框，輸入playwright後選擇install playwright

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1____XSOpdZvq__Vv8W4RKYw__FQ.png)

就會出現這些選項，比起剛剛用指令安裝的方式更有彈性

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__uftjgRlvgjHUiuAidrlb1A.png)

跑完指令之後，就會看到新生成的test資料夾和playwright config

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__7iKBOxxwsQp5ns66EaMt6A.png)

#### playwright config相關設定

接下來介紹一些playwright config基本的設定

  testDir: "./tests", // 跑測試的目標資料夾  
  timeout: 30 \* 1000, //如果在測試的時候卡住，超過timeout時間就fail  
   
  //ex: 預期在畫面上會出現一個login的按鈕，但過了30秒都偵測不到，就會判定測試失敗

  // 設定好baseURL之後 寫測試的時候就可以寫相對路徑囉，不用寫完整路徑  
  use: {  
    /\* Base URL to use in actions like \`await page.goto('/')\`. \*/  
    baseURL: 'http://127.0.0.1:3000',  
  },

#### **瀏覽器的版本**

可以自行定義要測試的瀏覽器有哪幾種，最貼心的居然還有手機版的瀏覽器！不過設定的瀏覽器越多也意謂著測試會跑越久，還是看專案的需求來決定要以哪些瀏覽器為主，通常會以chrome為主

  projects: \[  
    {  
      name: 'chromium',  
      use: { ...devices\['Desktop Chrome'\] },  
    },  
  
    {  
      name: 'firefox',  
      use: { ...devices\['Desktop Firefox'\] },  
    },  
  
    {  
      name: 'webkit',  
      use: { ...devices\['Desktop Safari'\] },  
    },  
  
    /\* Test against mobile viewports. \*/  
    {  
       name: 'Mobile Chrome',  
       use: { ...devices\['Pixel 5'\] },  
     },  
    {  
      name: 'Mobile Safari',  
      use: { ...devices\['iPhone 12'\] },  
    },

#### webserver

測試可能會有兩個情境

*   測試正在開發的網頁 ex. localhost:3000…
*   測試已經上線的網頁 ex. https://production.com

假如我們想要測試localhost的話，可以有兩種做法

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__FuXJ__So8HtRC8ZCA2aDlKQ.png)

1.  自己先在project B手動起一個localhost:3000，然後在project A的playwright的baseURL設定為localhost:3000，讓playwright針對這個localhost:3000 做測試
2.  我希望每次跑測試之前，playwright可以先自動幫我起一個localhost的網頁，當localhost 起好了之後，playwright再去測試這個頁面，所有的動作都在project A完成，為了達成這件事，我們就必須設定webserver

  //在跑測試的之前，先啟動一個網頁  
   webServer: {  
    command: 'pnpm run dev',  
    url: 'http://127.0.0.1:3000',  
    reuseExistingServer: !process.env.CI,  
  },

> 因為ci的部分我比較不熟，所以相關的設定就不會介紹了，敬請見諒

#### 強大的vscode擴充套件

安裝好了之後，左邊會出現ㄧ個燒杯(?)的icon，按下去就會出現測試的面板

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__f6LClFNPJQb2rpOTomO2DQ.png)

上方會列出所有的test case，資料夾結構一目暸然，只要按下title旁邊的播放icon，就會跑測試了，接下來會介紹下方面板提供的一些好用功能

#### Pick locator

看著網頁上琳瑯滿目的元素，不知道該下什麼語法才能選到目標元素嗎？就讓pick locator來處理吧！按下pick locator之後會開啟一個空白分頁，輸入網址後，游標指到的元素就會出現選擇語法的提示，是不是很貼心！！

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__KrOMTIeHaqO8qvdut41dCw.png)

#### Record new

和剛剛一樣會開啟一個空白頁面，從輸入網址到後續所有的操作行為，playwright都會錄製起來，我的操作步驟如下

1.  輸入網址
2.  點選帳號輸入框
3.  輸入帳號
4.  點選密碼輸入框
5.  輸入密碼
6.  按下送出按鈕

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__IAZn3l7rWBRLt2yJlg7oXA.png)

操作完畢後，回到vscode看會發現playwright已經幫你寫好了所有的步驟，完全不用親自動手，不過實務上還是會做微調，不然就會出現很多不必要的語法，像是input click這個動作其實是不需要的，只是因為我們在輸入之前要先選到那個輸入框，因此是可以被移除，只要留下fill和click button 的語法即可

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__ND6eVizGv__5pxNoYtBVc5g.png)

#### 登入授權處理

假如今天我們要測試的網頁，必須是要先登入，才能夠進到的頁面，那是否意謂著測試每個頁面之前都必須要打一次登入呢？流程會像下面這樣

//test.a.spec.ts  
test(login)...  
test(memberCenter)...  
  
//test.b.sepc.ts  
test(login)...  
test(settings)..

playwright提供了一個更好的做法，那就是在執行測試之前，先去跑登入的設定，取得cookie、token等資訊，然後存放在一個json檔裡面，所有的test case都去這個json裡面拿授權的資訊即可，方便很多

#### step 1

建立playwright/.auth資料夾，並且加入.gitignore

mkdir -p playwright/.auth  
echo "\\nplaywright/.auth" >> .gitignore

#### step 2

建立auth.setup.ts，撰寫登入的步驟

import { test as setup } from "@playwright/test";  
  
//指定authFile path  
const authFile = "playwright/.auth/user.json";  
  
setup("authenticate", async ({ page }) => {  
  await page.goto("/");  
  await page.locator('input\[name="account"\]').click();  
  await page.locator('input\[name="account"\]').fill("admin");  
  await page.locator('input\[name="account"\]').press("Tab");  
  await page.locator('input\[name="password"\]').fill("123456");  
  await page.getByRole("button", { name: "submit" }).click();  
  
  await page.waitForURL("/member");  
  
  //把拿到的token存放到authFile  
  await page.context().storageState({ path: authFile });  
});

#### step 3

打開playwright.config.ts 建立一個新的project setup，記得新增storageState，並且將要測試的瀏覽器dependencies設定為setup，這樣在跑測試之前都會先執行登入授權的動作，登入成功後，所有的test case都可以去playwright/.auth/user.json拿資料

  projects: \[  
    { name: "setup", testMatch: /.\*\\.setup\\.ts/ },  
    {  
      name: "chromium",  
      use: {  
        ...devices\["Desktop Chrome"\],  
        storageState: "playwright/.auth/user.json",  
      },  
      dependencies: \["setup"\],  
    },  
  \]

一切都準備就緒之後，就開始來跑測試吧！會發現不管是執行哪個測試檔，都會先去跑auth.setup.ts，假設登入成功，playwright會自動生成一個user.json檔放在playwright/.auth 底下，內容如下，不論api回傳的cookie或是localstorage裡面的資訊，通通存起來，讓所有的test case都能夠共享這auth資訊

//playwright/.auth/user.json的內容  
{  
  "cookies": \[  
    {  
      "name": "auth-token",  
      "value": "63099c80-773d-4bca-b2ba-bea12c9fb44e",  
      "domain": "localhost",  
      "path": "/",  
      "expires": \-1,  
      "httpOnly": false,  
      "secure": false,  
      "sameSite": "Lax"  
    }  
  \],  
  "origins": \[  
    {  
      "origin": "http://localhost:5176",  
      "localStorage": \[  
        {  
          "name": "MSW\_COOKIE\_STORE",  
          "value": "\[\[\\"http://localhost:5176\\",\[\[\\"auth-token\\",{\\"name\\":\\"auth-token\\",\\"value\\":\\"63099c80-773d-4bca-b2ba-bea12c9fb44e\\"}\]\]\]\]"  
        },  
        {  
          "name": "user",  
          "value": "admin"  
        }  
      \]  
    }  
  \]  
}

#### 結語

不過因為playwright比較年輕，所以在使用上確實容易遇到bug，像是近期在寫測試的時候，一樣的click執行程序, chrome、safari 都pass 唯獨firefox fail，最後去github issues看到別人也遇到同樣的情形([issue](https://github.com/microsoft/playwright/issues/21995))，不過還好近期官方也修正了，只是還沒release，抑或是vs code的playwright plugin會出現異常的狀況，完全無法執行測試，必須重開才會正常，但整體而言，playwright帶給我的開發體驗還是相當不錯的，非常推薦大家可以嘗試看看！