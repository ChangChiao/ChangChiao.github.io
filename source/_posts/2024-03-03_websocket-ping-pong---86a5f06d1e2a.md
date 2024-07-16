---
title: websocket ping pong機制
description: >-
  近期在工作上碰到一個產品需求是前後端必須保持長時間的連線，且當前端斷線的時候，後端也必須馬上知道前端斷線並且清除當前的連線，看了許多篇技術文章，發現不少人採用了websocket
  ping pong的方式來實作，websocket ping pong又被稱為websocket…
date: "2024-03-03T04:00:49.204Z"
categories: websocket
keywords: websocket
---

![](/img/1__L9wTpqQkEQ0OFd__U6U6cUQ.jpeg)

近期在工作上碰到一個產品需求是前後端必須保持長時間的連線，且當前端斷線的時候，後端也必須馬上知道前端斷線並且清除當前的連線，看了許多篇技術文章，發現不少人採用了 websocket ping pong 的方式來實作，websocket ping pong 又被稱為 websocket heart beat，目的是要讓前後端保持長時間的連線，確保前後端在連接 websocket 時，任一方關閉連線或是因為異常斷開連線，另一方都能即時知道，並且做出對應的處理（ex.重新連線）

> 如果前後端都是使用 socket io 開發，那基本上就不需要自己實作 ping pong，socket io 底層都幫你搞定了，不過假設前後端都是用原生的 websocket 開發，還是只能自己實作 ping pong

#### 什麼時候瀏覽器會自動斷開 websocket

當電腦休眠或是瀏覽器失焦超過一定的時間時， websocket 會斷開連線，因而觸發 websocket close 的事件，當偵測到 close 事件的時候，可以寫個 reconnect 的機制去重新連線，不過有個比較棘手的情況是當瀏覽器已經連上 websocket 之後，過沒多久網路斷線，此時前端的 websocket 並不會觸發 close 和 error 事件，因此前端無法嘗試重連、後端也不會知道前端已經斷線

這時就必須實作 ping pong 機制，就像是打乒乓球一樣，每隔幾秒後端就往前端送出 ping 訊息，前端也往後端送出 pong 的訊息，雖然比較耗網路資源，但能夠有效地確保當前的連線是否正常，當有其中一方過了一定的時間卻收不到對方傳來的訊息，基本上就能夠判斷對方已經斷線，可以做後續的斷線處理，以前端來說，超過 30 秒都還沒收到後端的 pong，就會嘗試重新連線

#### 實作 ping pong

前端的程式碼如下，在建立 websocket 之後會監聽連線成功、收到訊息、關閉連線、錯誤發生等事件，一般來說遇到伺服器端關閉連線(觸發 close 事件)或是 websocket 錯誤(觸發 error 事件)都會嘗試重新連線

```javascript
const connectWebSocket = () => {
  socket = new WebSocket("ws://localhost:8082");

  socket.addEventListener("open", (event) => {
    console.log("Connected to WebSocket server");
  });

  socket.addEventListener("message", (event) => {
    console.log("Message from server:", event.data);
    const msg = JSON.parse(event.data);
    if (msg.message === "ping") {
      socket.send(JSON.stringify({ message: "pong" }));
      handleWsCountDown();
    }
  });

  socket.addEventListener("error", (event) => {
    console.log("error", event);
    reconnect();
  });

  socket.addEventListener("close", () => {
    console.log("close connect");
    reconnect();
  });
};
```

並且在收到 ping 的訊息時的同時會設定一個 setTimeout，如果 30 秒內沒有再次收到 ping 訊息，意謂伺服器可能發生什麼問題，就會主動關閉 websocket 連線，然後觸發重新連線機制

```javascript
let serverTimeoutId = null;
const handleWsCountDown = () => {
  clearTimeout(serverTimeoutId);
  serverTimeoutId = setTimeout(() => {
    socket.close();
  }, 30000);
};
```

為了避免太過頻繁的 retry，一般來說會間隔幾秒才發出下一個連線請求

```javascript
let retryTimeoutId = null;

const reconnect = () => {
  clearTimeout(retryTimeoutId);
  retryTimeoutId = setTimeout(() => {
    connectWebSocket();
  }, 3000);
};
```

至於 ping pong，我看網路上有兩種做法

- 前端定期每 n 秒往後端送出 pong 的訊息
- 前端收到後端發來的 ping 訊息才回傳 pong 的訊息給後端

這邊採取第二種做法

```javascript
socket.addEventListener("message", (event) => {
  console.log("Message from server:", event.data);
  const msg = JSON.parse(event.data);
  if (msg.message === "ping") {
    socket.send(JSON.stringify({ message: "pong" }));
    handleWsCountDown();
  }
});
```

前端完整程式碼如下

```javascript
let serverTimeoutId = null;
let retryTimeoutId = null;
let socket = null;

const connectWebSocket = () => {
  socket = new WebSocket("ws://localhost:8082");

  socket.addEventListener("open", (event) => {
    console.log("Connected to WebSocket server");
    handlePong();
  });

  socket.addEventListener("message", (event) => {
    console.log("Message from server:", event.data);
    const msg = JSON.parse(event.data);
    if (msg.message === "ping") {
      socket.send(JSON.stringify({ message: "pong" }));
      handleWsCountDown();
    }
  });

  socket.addEventListener("error", (event) => {
    console.log("error", event);
    reconnect();
  });

  socket.addEventListener("close", () => {
    console.log("close connect");
    reconnect();
  });
};

const reconnect = () => {
  console.log("reconnect");
  clearTimeout(retryTimeoutId);
  retryTimeoutId = setTimeout(() => {
    connectWebSocket();
  }, 3000);
};

const handleWsCountDown = () => {
  clearTimeout(serverTimeoutId);
  serverTimeoutId = setTimeout(() => {
    socket.close();
  }, 10000);
};

connectWebSocket();
```

後端的部分則是使用 node.js，需要定時往前端送出 ping 的訊息，並且當超過一定時間沒收到前端發來的 pong，則視作前端已斷線，做後續的處理

```javascript
const WebSocket = require("ws");

const wss = new WebSocket.Server({ port: 8082 });

wss.on("connection", function connection(ws) {
  console.log("connection");

  ws.on("message", function message(data) {
    console.log("server-received==", data);
    // 若超過一定時間沒收到pong則視作前端已斷線，做後續處理...
  });
  const pingInterval = setInterval(() => {
    ws.send(JSON.stringify({ message: "ping" }));
  }, 3000);

  ws.on("close", function close() {
    clearInterval(pingInterval);
    console.log("disconnected");
  });
});
```

以上就是 websocket ping pong 的簡單介紹，如果看完有任何的想法都歡迎留言
