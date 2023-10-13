---
title: E2E test的明日之星 — playwright
description: >-
  說到E2E(End to
  End)測試工具，大部分的人應該第一個想到的會是Cypress，不過其實除了Cypress，還有一個好用的工具，那就是由微軟開發的後起之秀 — playwright，近期因為工作需求需要寫E2E…
date: "2023-07-22T10:52:17.110Z"
categories: e2eTest
tag: playwright
keywords: []
---

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__no__d95uHh8__Vu67iGJ52ug.jpeg)

說到 E2E(End to End)測試工具，大部分的人應該第一個想到的會是 Cypress，不過其實除了 Cypress，還有一個好用的工具，那就是由微軟開發的後起之秀  — playwright，近期因為工作需求需要寫 E2E test，便將 cypress 和 playwright 列入考量清單，雖然 cypress 歷史較為悠久但因為跑 ci 時耗費的資源較多，幾經評估之後我們團隊決定選擇 playwright，跟著官方的文件玩過一次，發現還蠻有趣的，接下來會介紹 playwright 有哪些特點

### Trace viewer —  動作錄製

在網頁上的所有操作行為， playwright 都能幫你錄製，當你按下錄製按鈕之後 playwright 會將你在網頁上的每一個操作步驟都寫成程式碼(如下圖)，完全不用自己動手

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__pytjxluVwr47OIk8STlrZw.png)

#### ui 介面操作

playwright 提供了 ui 介面方便使用者查看每個測試階段，左邊會列出測試步驟，上方則會有逐格的畫面錄製，可以觀察每個畫面的變化，下方的面板有 console、network 等等的資訊，讓開發者更容易除錯

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__LIgaw3PBp6tBD76M68MTig.png)

#### 測試報告

會依照 test case 和瀏覽器逐一生成測試報告，讓測試結果一目瞭然，後方也會列出執行秒數

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__ssnE0aA6z__NhGg__WR__JRdg.png)

點開標題，內頁會明確地告訴你測試的細節，哪個測試環節沒有 pass 等等

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__QzSjmFdSGwda6CrHaySMDA.png)

#### 小試身手

`環境配置：pnpm + svelte + mock server + playwright`

在開始寫測試之前，強烈推薦大家安裝 vscode playwright 套件，因為這個工具真的非常方便，後面會再做介紹

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__ZTaDMLChrhhYj6XWeNX5lw.png)

#### 如何開始

在一個既有的專案根目錄底下執行指令，使用 npm 和 yarn 的朋友請看[官網的指令](https://playwright.dev/docs/intro)，和 pnpm 的有些許差異

```bash
pnpm dlx create-playwright
```

接下來會詢問你一些問題，可根據自身需求做調整

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__qTkJv1e2YWtUK0QtHA2rYw.png)

不想用 command line 的話，也可以用另一個方式安裝 playwright

假如先前已經安裝好 playwright vscode 套件，可以按 ctrl(cmd) + shift + p，呼叫執行指令的輸入框，輸入 playwright 後選擇 install playwright

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1____XSOpdZvq__Vv8W4RKYw__FQ.png)

就會出現這些選項，比起剛剛用指令安裝的方式更有彈性

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__uftjgRlvgjHUiuAidrlb1A.png)

跑完指令之後，就會看到新生成的 test 資料夾和 playwright config

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__7iKBOxxwsQp5ns66EaMt6A.png)

#### playwright config 相關設定

接下來介紹一些 playwright config 基本的設定

```json
testDir: "./tests", // 跑測試的目標資料夾
  timeout: 30 * 1000, //如果在測試的時候卡住，超過timeout時間就fail

  //ex: 預期在畫面上會出現一個login的按鈕，但過了30秒都偵測不到，就會判定測試失敗
```

```json
  // 設定好baseURL之後 寫測試的時候就可以寫相對路徑囉，不用寫完整路徑
  use: {
    /* Base URL to use in actions like `await page.goto('/')`. */
    baseURL: 'http://127.0.0.1:3000',
  },

```

#### **瀏覽器的版本**

可以自行定義要測試的瀏覽器有哪幾種，最貼心的居然還有手機版的瀏覽器！不過設定的瀏覽器越多也意謂著測試會跑越久，還是看專案的需求來決定要以哪些瀏覽器為主，通常會以 chrome 為主

```javascript
projects: [
  {
    name: "chromium",
    use: { ...devices["Desktop Chrome"] },
  },

  {
    name: "firefox",
    use: { ...devices["Desktop Firefox"] },
  },

  {
    name: "webkit",
    use: { ...devices["Desktop Safari"] },
  },

  /* Test against mobile viewports. */
  {
    name: "Mobile Chrome",
    use: { ...devices["Pixel 5"] },
  },
  {
    name: "Mobile Safari",
    use: { ...devices["iPhone 12"] },
  },
];
```

#### webserver

測試可能會有兩個情境

- 測試正在開發的網頁 ex. localhost:3000…
- 測試已經上線的網頁 ex. https://production.com

假如我們想要測試 localhost 的話，可以有兩種做法

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__FuXJ__So8HtRC8ZCA2aDlKQ.png)

1.  自己先在 project B 手動起一個 localhost:3000，然後在 project A 的 playwright 的 baseURL 設定為 localhost:3000，讓 playwright 針對這個 localhost:3000 做測試
2.  我希望每次跑測試之前，playwright 可以先自動幫我起一個 localhost 的網頁，當 localhost 起好了之後，playwright 再去測試這個頁面，所有的動作都在 project A 完成，為了達成這件事，我們就必須設定 webserver

```javascript
//在跑測試的之前，先啟動一個網頁
 webServer: {
 command: 'pnpm run dev',
 url: 'http://127.0.0.1:3000',
 reuseExistingServer: !process.env.CI,
 },
```

> 因為 ci 的部分我比較不熟，所以相關的設定就不會介紹了，敬請見諒

#### 強大的 vscode 擴充套件

安裝好了之後，左邊會出現ㄧ個燒杯(?)的 icon，按下去就會出現測試的面板

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__f6LClFNPJQb2rpOTomO2DQ.png)

上方會列出所有的 test case，資料夾結構一目暸然，只要按下 title 旁邊的播放 icon，就會跑測試了，接下來會介紹下方面板提供的一些好用功能

#### Pick locator

看著網頁上琳瑯滿目的元素，不知道該下什麼語法才能選到目標元素嗎？就讓 pick locator 來處理吧！按下 pick locator 之後會開啟一個空白分頁，輸入網址後，游標指到的元素就會出現選擇語法的提示，是不是很貼心！！

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__KrOMTIeHaqO8qvdut41dCw.png)

#### Record new

和剛剛一樣會開啟一個空白頁面，從輸入網址到後續所有的操作行為，playwright 都會錄製起來，我的操作步驟如下

1.  輸入網址
2.  點選帳號輸入框
3.  輸入帳號
4.  點選密碼輸入框
5.  輸入密碼
6.  按下送出按鈕

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__IAZn3l7rWBRLt2yJlg7oXA.png)

操作完畢後，回到 vscode 看會發現 playwright 已經幫你寫好了所有的步驟，完全不用親自動手，不過實務上還是會做微調，不然就會出現很多不必要的語法，像是 input click 這個動作其實是不需要的，只是因為我們在輸入之前要先選到那個輸入框，因此是可以被移除，只要留下 fill 和 click button 的語法即可

![](/Users/joectchang_mac/Downloads/medium-export-a/post2023/md_1697073963636/img/1__ND6eVizGv__5pxNoYtBVc5g.png)

#### 登入授權處理

假如今天我們要測試的網頁，必須是要先登入，才能夠進到的頁面，那是否意謂著測試每個頁面之前都必須要打一次登入呢？流程會像下面這樣

```javascript
//test.a.spec.ts
test(login)...
test(memberCenter)...

//test.b.sepc.ts
test(login)...
test(settings)..
```

playwright 提供了一個更好的做法，那就是在執行測試之前，先去跑登入的設定，取得 cookie、token 等資訊，然後存放在一個 json 檔裡面，所有的 test case 都去這個 json 裡面拿授權的資訊即可，方便很多

#### step 1

建立 playwright/.auth 資料夾，並且加入.gitignore

```bash
mkdir -p playwright/.auth
echo "\\nplaywright/.auth" >> .gitignore
```

#### step 2

建立 auth.setup.ts，撰寫登入的步驟

```javascript
import { test as setup } from "@playwright/test";

//指定 authFile path
const authFile = "playwright/.auth/user.json";

setup("authenticate", async ({ page }) => {
  await page.goto("/");
  await page.locator('input[name="account"]').click();
  await page.locator('input[name="account"]').fill("admin");
  await page.locator('input[name="account"]').press("Tab");
  await page.locator('input[name="password"]').fill("123456");
  await page.getByRole("button", { name: "submit" }).click();

  await page.waitForURL("/member");

  //把拿到的 token 存放到 authFile
  await page.context().storageState({ path: authFile });
});
```

#### step 3

打開 playwright.config.ts 建立一個新的 project setup，記得新增 storageState，並且將要測試的瀏覽器 dependencies 設定為 setup，這樣在跑測試之前都會先執行登入授權的動作，登入成功後，所有的 test case 都可以去 playwright/.auth/user.json 拿資料

```javascript
projects: [
  { name: "setup", testMatch: /.*\.setup\.ts/ },
  {
    name: "chromium",
    use: {
      ...devices["Desktop Chrome"],
      storageState: "playwright/.auth/user.json",
    },
    dependencies: ["setup"],
  },
];
```

一切都準備就緒之後，就開始來跑測試吧！會發現不管是執行哪個測試檔，都會先去跑 auth.setup.ts，假設登入成功，playwright 會自動生成一個 user.json 檔放在 playwright/.auth 底下，內容如下，不論 api 回傳的 cookie 或是 localstorage 裡面的資訊，通通存起來，讓所有的 test case 都能夠共享這 auth 資訊

```javascript
//playwright/.auth/user.json 的內容
{
  "cookies": [
    {
      "name": "auth-token",
      "value": "63099c80-773d-4bca-b2ba-bea12c9fb44e",
      "domain": "localhost",
      "path": "/",
      "expires": -1,
      "httpOnly": false,
      "secure": false,
      "sameSite": "Lax"
    }
  ],
  "origins": [
    {
      "origin": "http://localhost:5176",
      "localStorage": [
        {
          "name": "MSW_COOKIE_STORE",
          "value": "[[\"http://localhost:5176\",[[\"auth-token\",{\"name\":\"auth-token\",\"value\":\"63099c80-773d-4bca-b2ba-bea12c9fb44e\"}]]]]"
        },
        {
          "name": "user",
          "value": "admin"
        }
      ]
    }
  ]
}
```

#### 結語

不過因為 playwright 比較年輕，所以在使用上確實容易遇到 bug，像是近期在寫測試的時候，一樣的 click 執行程序, chrome、safari 都 pass 唯獨 firefox fail，最後去 github issues 看到別人也遇到同樣的情形([issue](https://github.com/microsoft/playwright/issues/21995))，不過還好近期官方也修正了，只是還沒 release，抑或是 vs code 的 playwright plugin 會出現異常的狀況，完全無法執行測試，必須重開才會正常，但整體而言，playwright 帶給我的開發體驗還是相當不錯的，非常推薦大家可以嘗試看看！
