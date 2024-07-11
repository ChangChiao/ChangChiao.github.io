---
title: 讓Angular reactive起來 — Signal
description: >-
  Signal在angular
  v17正式發佈，意謂著angular進入了一個新的世代，一直以來angular更新view都是依賴zone.js，但效能問題一直是一個痛點，zone.js必須地毯式的搜尋哪裡有變更，從component…
date: '2024-06-30T05:26:22.176Z'
categories: []
keywords: []
slug: /@joe-chang/%E8%AE%93angular-reactive%E8%B5%B7%E4%BE%86-signal-139a8dce1c8e
---

![](/Users/joectchang_mac/Downloads/export/md_1720440695883/img/1__OLKklASWmFQ__S72rfLelaw.jpeg)

Signal在angular v17正式發佈，意謂著angular進入了一個新的世代，一直以來angular更新view都是依賴zone.js，但效能問題一直是一個痛點，zone.js必須地毯式的搜尋哪裡有變更，從component tree的根部出發，一直掃描到最底層的子component才能知道哪邊的view需要變更，但是signal不同，在茫茫的component海之中，假設有view需要重新渲染的時候，Signal會主動舉手說✋：「嘿！我這裡需要更新」，因此更新的效率比zone.js好上不少，在效能上也有所提升

> Signal的概念其實早在Preact、Qwik、SoidJS中就有被提到過

以往angular如果想要做到資料響應式的效果就必須仰賴RxJS 的observeble，但除了學習曲線高之外，而且在subscribe之後還要記得處理unsubscribe， 不然會有memory leak的風險，整體而言對於開發者的心智負擔有點高，而signal的優勢是效能更好，更直觀的命令式的寫法讓人更好入門，對於曾經寫過vue的開發者來說備感親切，幾乎可以說是無痛上手 😆

> Signal可以取代RxJS嗎？

> 我個人覺得Signal的確可以取代一部分RxJS的功能，但無法完全取代，雖然說Signal的響應式真的很棒，但不得不說在處理一連串資料流的時候，RxJS還是有它的優勢在，個人的看法是未來Signal和rxjs會是共存的狀態，開發者會根據使用情境來決定要採用哪種資料處理的方式

#### Signal

會宣告為WritableSignal的類型，讓開發者清楚知道這是個可以寫入的Signal，並且可以透過set、update等方法來更新signal

todoList: WritableSignal<string\[\]> = signal(\['sleep', 'eat', 'gaming'\])  
  
//set 直接覆寫  
todoList.set(\['snooze'\])  
  
//update 根據先前的值做修改  
todoList.update((value) => ({...value, 'shopping'})\]  
  

在html template或是ts裡面使用signal要記得要加上()，像是call一個function那樣

// html  
<ul\>  
@for(item of todoList()){  
  <li\>{{ item }}</li\>  
}  
</ul\>

// ts file  
getTodoList(){  
  console.log('todo', this.todoList())  
}

#### Computed

當Signal的值變化時會重新計算，響應式的回傳值

todoListCount = computed(() => this.todoList().length)

在template的使用方式也需要使用()

<span\> {{ count() }} </span\>

⚠️ computed的值是唯讀的，無法修改

#### effect

這裡就跟vue的watchEffect很類似了，會監聽在裡面用到的signal變數，如果有變化就會執行function

effect(()) => {  
  console.log('todoList', this.todoList)  
})  
  

假設在effect裡面有用到side effect的function ，可以使用cleanup來清除

effect((onCleanup) => {  
  const timer = setTimeout(() => {  
    console.log(\`log todoList ${this.todoList()}\`);  
  }, 2000);  
  
  onCleanup(() => {  
    clearTimeout(timer);  
  });  
});

#### toSignal

可以將observable轉換成Signal，以下是將ngrx store的select轉換為Signal的例子

#store = inject(Store)  
  
memberData = toSignal(this.#store.select(shoppingList), { initialValue: \[\] })  
  
amount = computed(()) => this.memberData.reduce(sum, item) => sum+ item.price, 0)

#### ngrx signal Store

有了Signal store，寫component store或global store不再那麼痛苦了，拋開action、reducer、effect，簡化了許多，可以少寫非常多code!！雖然目前還是developer preview的階段，但相信很快就會變成stable了！

export const shoppingStore = signalStore(  
  withState({  
    cart: \[\],  
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

#### Signal input

以往當input有變化的時候，都是仰賴ngOnChanges來做後續處理，現在input變成Signal之後，就可以搭配effect來使用了

// old    
export class TodoList {  
  @Input() list: string\[\] = \[\]  
  
}  
  
// new  
export class TodoList {  
  list = input<string\[\]> = \[\]  
}

#### Signal ouput

簡潔的寫法就是讚

// old    
export class TodoList {  
  @Output() save = new EventEmitter<string\[\]>();  
}  
  
// new  
export class TodoList {  
  save = output<string\[\]> = \[\]  
}

Signal的出現，無疑是angular新手的福音，熟悉的命令式寫法，更低的學習成本，目前在專案開發上，Signal帶來的開發體驗的確是相當不錯，推薦大家有機會可以試試看！