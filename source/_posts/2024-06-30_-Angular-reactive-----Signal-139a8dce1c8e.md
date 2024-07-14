---
title: 讓Angular reactive起來 — Signal
description: >-
  Signal在angular
  v17正式發佈，意謂著angular進入了一個新的世代，一直以來angular更新view都是依賴zone.js，但效能問題一直是一個痛點，zone.js必須地毯式的搜尋哪裡有變更，從component…
date: "2024-06-30T05:26:22.176Z"
categories: "angular"
keywords: "angular"
---

![](/img/1__OLKklASWmFQ__S72rfLelaw.jpeg)

Signal 在 angular v17 正式發佈，意謂著 angular 進入了一個新的世代，一直以來 angular 更新 view 都是依賴 zone.js，但效能問題一直是一個痛點，zone.js 必須地毯式的搜尋哪裡有變更，從 component tree 的根部出發，一直掃描到最底層的子 component 才能知道哪邊的 view 需要變更，但是 signal 不同，在茫茫的 component 海之中，假設有 view 需要重新渲染的時候，Signal 會主動舉手說 ✋：「嘿！我這裡需要更新」，因此更新的效率比 zone.js 好上不少，在效能上也有所提升

> Signal 的概念其實早在 Preact、Qwik、SoidJS 中就有被提到過

以往 angular 如果想要做到資料響應式的效果就必須仰賴 RxJS 的 observeble，但除了學習曲線高之外，而且在 subscribe 之後還要記得處理 unsubscribe， 不然會有 memory leak 的風險，整體而言對於開發者的心智負擔有點高，而 signal 的優勢是效能更好，更直觀的命令式的寫法讓人更好入門，對於曾經寫過 vue 的開發者來說備感親切，幾乎可以說是無痛上手 😆

> Signal 可以取代 RxJS 嗎？

> 我個人覺得 Signal 的確可以取代一部分 RxJS 的功能，但無法完全取代，雖然說 Signal 的響應式真的很棒，但不得不說在處理一連串資料流的時候，RxJS 還是有它的優勢在，個人的看法是未來 Signal 和 rxjs 會是共存的狀態，開發者會根據使用情境來決定要採用哪種資料處理的方式

#### Signal

會宣告為 WritableSignal 的類型，讓開發者清楚知道這是個可以寫入的 Signal，並且可以透過 set、update 等方法來更新 signal

```javascript
todoList: WritableSignal<string[]> = signal(['sleep', 'eat', 'gaming'])

//set 直接覆寫
todoList.set(['snooze'])

//update 根據先前的值做修改
todoList.update((value) => ({...value, 'shopping'}))


```

在 html template 或是 ts 裡面使用 signal 要記得要加上()，像是 call 一個 function 那樣

```html
<!-- html -->
<ul>
  @for(item of todoList()){
  <li>{{ item }}</li>
  }
</ul>
```

```javascript
// ts file
getTodoList(){
 console.log('todo', this.todoList())
}
```

#### Computed

當 Signal 的值變化時會重新計算，響應式的回傳值

```javascript
todoListCount = computed(() => this.todoList().length);
```

在 template 的使用方式也需要使用()

```html
<span> {{ count() }} </span>
```

⚠️ computed 的值是唯讀的，無法修改

#### effect

這裡就跟 vue 的 watchEffect 很類似了，會監聽在裡面用到的 signal 變數，如果有變化就會執行 function

```javascript
effect(()) => {
 console.log('todoList', this.todoList)
})

```

假設在 effect 裡面有用到 side effect 的 function ，可以使用 cleanup 來清除

```javascript
effect((onCleanup) => {
  const timer = setTimeout(() => {
    console.log(`log todoList ${this.todoList()}`);
  }, 2000);

  onCleanup(() => {
    clearTimeout(timer);
  });
});
```

#### toSignal

可以將 observable 轉換成 Signal，以下是將 ngrx store 的 select 轉換為 Signal 的例子

```javascript

#store = inject(Store)

memberData = toSignal(this.#store.select(shoppingList), { initialValue: [] })

amount = computed(()) => this.memberData.reduce(sum, item) => sum+ item.price, 0)


```

#### ngrx signal Store

有了 Signal store，寫 component store 或 global store 不再那麼痛苦了，拋開 action、reducer、effect，簡化了許多，可以少寫非常多 code!！雖然目前還是 developer preview 的階段，但相信很快就會變成 stable 了！

```javascript

export const shoppingStore = signalStore(
  withState({
    cart: [],
    coupon: 1,
  }),
  withComputed(({cart}))=>{
    amount: computed(()) => this.memberData.reduce(sum, item) => sum+ item.price, 0)
  },
  withMethods((store)) => {
    updateCart(payload){
      patchState(store, (state) => cart: payload)
    }
  })
)


```

#### Signal input

以往當 input 有變化的時候，都是仰賴 ngOnChanges 來做後續處理，現在 input 變成 Signal 之後，就可以搭配 effect 來使用了

```javascript
// old
export class TodoList {
  @Input() list: string[] = []

}

// new
export class TodoList {
  list = input<string[]> = []
}

```

#### Signal ouput

簡潔的寫法就是讚

```javascript
// old
export class TodoList {
  @Output() save = new EventEmitter<string[]>();
}

// new
export class TodoList {
  save = output<string[]> = []
}

```

Signal 的出現，無疑是 angular 新手的福音，熟悉的命令式寫法，更低的學習成本，目前在專案開發上，Signal 帶來的開發體驗的確是相當不錯，推薦大家有機會可以試試看！
