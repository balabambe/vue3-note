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

##. 特性的部份 (From [重新認識 Vue.js | Kuro Hsu](https://book.vue.tw/CH1/1-1-introduction.html#vue-js-3-0-%E6%96%B0%E5%A2%9E%E7%9A%84%E7%89%B9%E6%80%A7))
- 引進了 "Fragment" 特性，不再限制元件必須只能是單一根節點
- 狀態的響應式偵測由 `Object.defineProperty` 改為 `Proxy API`，執行時的效能更好，也解決了過去 Vue 2.x 在物件、陣列更新偵測的問題
- 模板編譯：靜態節點優化，新的 Virtual DOM 更新時只需要遍歷動態的節點，更新時與實際 DOM 數量脫鉤，減少不必要的效能浪費
- 內建新增 "Teleport"、"Suspense" 等功能型元件，擴充了更多可能性
- 新的 Composition API 提供了另一種對程式碼、邏輯甚至是狀態的共用管理方式，擺脫過去 mixins 的混亂
- 新增 `setup`、`ref` 等語法糖，開發時的體驗更好

##. data 的部份強制使用 function 回傳
##. 三次法則
##. Computed 有變數異動的偵測，還有變數計算後的暫存
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
##.v-model 的修飾子
1. lazy
1. number
1. trim

##. v-on 的修飾子
1. stop: stopPropagation()
1. prevent: preventDefault()
1. capture: 
1. self
1. once
1. passive