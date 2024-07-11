---
title: websocket ping pong機制
description: >-
  近期在工作上碰到一個產品需求是前後端必須保持長時間的連線，且當前端斷線的時候，後端也必須馬上知道前端斷線並且清除當前的連線，看了許多篇技術文章，發現不少人採用了websocket
  ping pong的方式來實作，websocket ping pong又被稱為websocket…
date: '2024-03-03T04:00:49.204Z'
categories: []
keywords: []
slug: /@joe-chang/websocket-ping-pong%E6%A9%9F%E5%88%B6-86a5f06d1e2a
---

![](/Users/joectchang_mac/Downloads/export/md_1720440695883/img/1__L9wTpqQkEQ0OFd__U6U6cUQ.jpeg)

近期在工作上碰到一個產品需求是前後端必須保持長時間的連線，且當前端斷線的時候，後端也必須馬上知道前端斷線並且清除當前的連線，看了許多篇技術文章，發現不少人採用了websocket ping pong的方式來實作，websocket ping pong又被稱為websocket heart beat，目的是要讓前後端保持長時間的連線，確保前後端在連接websocket時，任一方關閉連線或是因為異常斷開連線，另一方都能即時知道，並且做出對應的處理（ex.重新連線）

> 如果前後端都是使用socket io開發，那基本上就不需要自己實作ping pong，socket io底層都幫你搞定了，不過假設前後端都是用原生的websocket開發，還是只能自己實作ping pong

#### 什麼時候瀏覽器會自動斷開websocket

當電腦休眠或是瀏覽器失焦超過一定的時間時， websocket會斷開連線，因而觸發websocket close的事件，當偵測到close事件的時候，可以寫個reconnect的機制去重新連線，不過有個比較棘手的情況是當瀏覽器已經連上websocket之後，過沒多久網路斷線，此時前端的websocket並不會觸發close和error事件，因此前端無法嘗試重連、後端也不會知道前端已經斷線

這時就必須實作ping pong機制，就像是打乒乓球一樣，每隔幾秒後端就往前端送出ping訊息，前端也往後端送出pong的訊息，雖然比較耗網路資源，但能夠有效地確保當前的連線是否正常，當有其中一方過了一定的時間卻收不到對方傳來的訊息，基本上就能夠判斷對方已經斷線，可以做後續的斷線處理，以前端來說，超過30秒都還沒收到後端的pong，就會嘗試重新連線

#### 實作ping pong

前端的程式碼如下，在建立websocket之後會監聽連線成功、收到訊息、關閉連線、錯誤發生等事件，一般來說遇到伺服器端關閉連線(觸發close事件)或是websocket錯誤(觸發error事件)都會嘗試重新連線

    const connectWebSocket = () => {  
      socket = new WebSocket("ws://localhost:8082");  
  
      socket.addEventListener("open", (event) => {  
        console.log("Connected to WebSocket server");  
      });  
  
      socket.addEventListener("message", (event) => {  
        console.log("Message from server:", event.data);  
        const msg = JSON.parse(event.data);  
        if (msg.message === "ping") {  
          socket.send(JSON.stringify({message: "pong"}));  
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

並且在收到ping的訊息時的同時會設定一個setTimeout，如果30秒內沒有再次收到ping訊息，意謂伺服器可能發生什麼問題，就會主動關閉websocket連線，然後觸發重新連線機制

let serverTimeoutId = null;      
const handleWsCountDown = () => {  
    clearTimeout(serverTimeoutId);  
    serverTimeoutId = setTimeout(() => {  
      socket.close();  
    }, 30000);  
  };

為了避免太過頻繁的retry，一般來說會間隔幾秒才發出下一個連線請求

    let retryTimeoutId = null;  
  
    const reconnect = () => {  
      clearTimeout(retryTimeoutId);  
      retryTimeoutId = setTimeout(() => {  
        connectWebSocket();  
      }, 3000);  
    };

至於ping pong，我看網路上有兩種做法

*   前端定期每n秒往後端送出pong的訊息
*   前端收到後端發來的ping訊息才回傳pong的訊息給後端

這邊採取第二種做法

      socket.addEventListener("message", (event) => {  
        console.log("Message from server:", event.data);  
        const msg = JSON.parse(event.data);  
        if (msg.message === "ping") {  
          socket.send(JSON.stringify({message: "pong"}));            
          handleWsCountDown();  
        }  
      });

前端完整程式碼如下

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
          socket.send(JSON.stringify({message: "pong"}));  
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

後端的部分則是使用node.js，需要定時往前端送出ping的訊息，並且當超過一定時間沒收到前端發來的pong，則視作前端已斷線，做後續的處理

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

以上就是websocket ping pong的簡單介紹，如果看完有任何的想法都歡迎留言