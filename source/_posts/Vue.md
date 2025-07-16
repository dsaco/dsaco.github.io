---
title: Vue
date: 2025-07-15 15:12:36
tags:
---

### vue路由里使用keep-alive

[官方文档](https://vuejs.org/guide/built-ins/keep-alive.html#keepalive)

> include指的是组件声明的name，单文件组件会自动推导 即 HomeView.vue => name: "HomeView"

```js
<router-view v-slot="{ Component }">
  <keep-alive include="HomeView">
    <component :is="Component" />
  </keep-alive>
</router-view>
```

