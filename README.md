# Vue 2 到 Vue 3 的差異筆記
## Vue 2 & Vue 3
Vue 2
```javascript
const vm = new Vue({
  data: {
    message: 'Hello World',
  }
}).$mount('#app');
```
Vue 3
```javascript
import { createApp } from 'vue';

const vm = Vue.createApp({
  data() {
    return {
      message: 'Hello World',
    }
  }
}).mount('#app');
```

## 特性的部份 (From [重新認識 Vue.js | Kuro Hsu](https://book.vue.tw/CH1/1-1-introduction.html#vue-js-3-0-%E6%96%B0%E5%A2%9E%E7%9A%84%E7%89%B9%E6%80%A7))
- 引進了 "Fragment" 特性，不再限制元件必須只能是單一根節點
- 狀態的響應式偵測由 `Object.defineProperty` 改為 `Proxy API`，執行時的效能更好，也解決了過去 Vue 2.x 在物件、陣列更新偵測的問題
- 模板編譯：靜態節點優化，新的 Virtual DOM 更新時只需要遍歷動態的節點，更新時與實際 DOM 數量脫鉤，減少不必要的效能浪費
- 內建新增 "Teleport"、"Suspense" 等功能型元件，擴充了更多可能性
- 新的 Composition API 提供了另一種對程式碼、邏輯甚至是狀態的共用管理方式，擺脫過去 mixins 的混亂
- 新增 `setup`、`ref` 等語法糖，開發時的體驗更好

## data 的部份強制使用 function 回傳
## 三次法則
[https://book.vue.tw/CH1/1-3-computed-and-methods.html#methods-%E6%96%B9%E6%B3%95](https://book.vue.tw/CH1/1-3-computed-and-methods.html#methods-%E6%96%B9%E6%B3%95)
## Computed 有變數異動的偵測，還有變數計算後的暫存
```html
<template>
  <p>{{ subTotalComputed }}</p>
  <p>{{ subTotalComputed }}</p>
  <p>{{ subTotalComputed }}</p>

  <p>{{ subTotal() }}</p>
  <p>{{ subTotal() }}</p>
  <p>{{ subTotal() }}</p>
</template>

<script>
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'Example',
  data() {
    return {
      a: 4,
      b: 6,
    };
  },
  computed: {
    subTotalComputed() {
      console.log('subTotalComputed');
      return this.a * this.b;
    },
  },
  methods: {
    subTotal() {
      console.log('subTotal');
      return this.a * this.b;
    },
  },
});
</script>

```
## v-model 的修飾子
1. lazy
1. number
1. trim

## v-on 的修飾子
1. stop: stopPropagation()
1. prevent: preventDefault()
1. capture: 指定某元素先觸發
1. self: 只觸發自己的元素，不會因為冒泡事件再連動底層元素
1. once: 事件只會觸發一次
1. passive: 用來略過 preventDefault 的事件

## event.key 修飾子
1. .enter
1. .esc
1. .tab
1. .space
1. .delete
1. .up
1. .down
1. .alt
1. .left
1. .right
1. .shift
1. .meta: 對應的是 win 鍵 & CMD 鍵
1. .ctrl

- keycode 的部份已經從網頁標準中移除，故 Vue 3 也不支援
- 任何修飾子再加上 .exact 會做精確判斷

## 陣列內容變動，畫面為何沒更新？(For Vue 2.x)
[看這裡](https://book.vue.tw/CH1/1-6-conditions-and-flow-control.html#%E8%A3%9C%E5%85%85-%E7%95%B6%E9%99%A3%E5%88%97%E7%9A%84%E5%85%A7%E5%AE%B9%E8%AE%8A%E5%8B%95-%E7%95%AB%E9%9D%A2%E7%82%BA%E4%BD%95%E6%B2%92%E6%9B%B4%E6%96%B0-vue-2-x)

或是改用這個方法更新：
```javascript
this.$set(this.books, idx, val);
```

## 等待畫面更新結束才執行(取代 setTimeout、setInterval 的方式)
```javascript
this.$nextTick(() => {
  ...
})
```
## 使用 v-bind 的方式將物件解構給子元件

## reset data 回復至初始資料的方式
```javascript
methods: {
  resetData() {
    Object.assign(this.$data, this.$options.data());
  }
}
```

## Props 資料類型驗證
1. String
1. Array
1. Function
1. Number
1. Object
1. Symbol
1. Boolean
1. Date

可以同時允許兩種型別傳入：
```javascript
props: {
  something: {
    type: [String, Number],
  },
},
```

props 可以用 validator 做驗證
```javascript
props: {
  something: {
    type: Number,
    validator: value => value > 0,
  }
}
```

## Props in setup
```javascript
// MyBook.vue

export default {
  props: {
    title: String
  },
  setup(props) {
    console.log(props.title)
  }
}
```
注意：props 不建議使用 es6 解構方式取出要的參數，因為 props 的內容是變動的(reactive)，如果使用了解構，props 的 reactive 會解除，如果有需要用解構，請使用 `toRefs`

```javascript
// MyBook.vue

import { toRefs } from 'vue'

setup(props) {
  const { title } = toRefs(props)

  console.log(title.value)
}
```
若 `title` 有可能不存在(Optional)，則請使用 `toRef` (少一個s)
```javascript
// MyBook.vue

import { toRef } from 'vue'

setup(props) {
  const title = toRef(props, 'title')

  console.log(title.value)
}
```

## 遞迴 Component (用在樹狀選單)
[看這裡](https://book.vue.tw/CH2/2-2-communications.html#props-%E8%88%87%E9%81%9E%E8%BF%B4%E5%85%83%E4%BB%B6)

## 跨越層級的傳遞方式
[看這裡](https://book.vue.tw/CH2/2-2-communications.html#%E8%B7%A8%E8%B6%8A%E5%B1%A4%E7%B4%9A%E7%9A%84%E5%82%B3%E9%81%9E%E6%96%B9%E5%BC%8F)

## teleport (Vue 3 新增)
作用於類似 lightbox 之類的燈箱操作上，因為背景遮罩的關係，可以用
```html
<teleport to="body">
  ...
</teleport>
```
的方式把 component 渲染在 body 之下

## router-view 可以組合
[看這裡](https://book.vue.tw/CH4/4-2-route-settings.html#%E5%B7%A2%E7%8B%80%E5%85%B7%E5%90%8D%E8%A6%96%E5%9C%96)

## vue-router
vue router 4 的 next() 已非必要呼叫，可省略

## Vuex 4.x 可以向下相容 Vue 3.x

## mixins Vue 3 不建議使用

## css deep selector 的改變
- ::v-deep(selector) {}
- ::v-global {}
