---
title: 認識 vue css scoped
date: "2020-05-04T14:02:13.102Z"
description: >-
  想必大家都有過一個經驗，多人開發撰寫css時，一個不小心就會發生樣式互蓋的情形，因此在vue裡面scoped可以幫助我們避免掉這樣的狀況，可以限定css的作用範圍，打開f12來看有使用scoped
  的component，可以發現在DOM和css的data屬性會加上data +…
categories: vue
keywords: []
tags: vue css
---

![](/img/1__QCFDDzRri5TbYnRWefOV9w.jpeg)

想必大家都有過一個經驗，多人開發撰寫 css 時，一個不小心就會發生樣式互蓋的情形，因此在 vue 裡面 scoped 可以幫助我們避免掉這樣的狀況，可以限定 css 的作用範圍，打開 f12 來看有使用 scoped 的 component，可以發現在 DOM 和 css 的 data 屬性會加上 `data + v + 亂數值`（類似 id 確保不重複）透過這樣的方式就可以讓 css 不互相影響。

```html
<!-- html -->
<div class="example" data-v-f3f3eg9>hi</div>
```

```css
/* css */
example[data-v-f3f3eg9] {
  color: red;
}
```

**什麼是 scoped**

在 style 標籤上加上 scoped ，撰寫的 css(sass)就只會作用於這個 component，即使選擇器重複，也不會影響到其他 component。

但想必曾經有遇過一個情況是在父組件寫了 scoped，但是子組件吃不到父組件 css 的狀況。以下方例子來說，helloworld component 是無法吃到 `h1{color：olivedrab}`的，這時候就可以使用 **/deep/** （只適用於 scss/sass）如此一來，樣式就可以穿透到子組件。

```javascript
<template>

   <div id="app">
     <img alt="Vue logo" src="./assets/logo.png">

     <HelloWorld />

   </div>

</template>

<script>

import HelloWorld from './components/HelloWorld.vue';

export default {

 name: 'App',

 components: {

   HelloWorld,

 },

};

</script>

<style lang="scss" scoped>

#app {

 font-family: Avenir, Helvetica, Arial, sans-serif;

 -webkit-font-smoothing: antialiased;

 -moz-osx-font-smoothing: grayscale;

 text-align: center;

 color: #2c3e50;

 margin-top: 60px;

 & /deep/ h1{

 color: olivedrab;

}

}

</style>
```

假如是用單純的 css 開發，使用`>>>`就可以讓底下的組件吃到 css

```css
#app >>> h1 {
  color: olivedrab;
}
```

之前開發為了要讓子組件吃到 css，就傻傻拿掉 scoped 了，最近才發現原來有穿透這樣的寫法，也算是學到了一課。
