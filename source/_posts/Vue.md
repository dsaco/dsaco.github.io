---
title: Vue
date: 2025-07-15 15:12:36
tags:
---

### vue 路由里使用 keep-alive

[官方文档](https://vuejs.org/guide/built-ins/keep-alive.html#keepalive)

> include 指的是组件声明的 name，单文件组件会自动推导 即 HomeView.vue => name: "HomeView"

```js
<router-view v-slot="{ Component }">
  <keep-alive include="HomeView">
    <component :is="Component" />
  </keep-alive>
</router-view>
```

> 如果是文件夹下的 index.vue 文件可以手动指定

```js
defineOptions({
  name: 'CompName',
});
```

### ant-design-vue

#### 编辑器代码提示

```ts
// vite.config.ts
import Components from 'unplugin-vue-components/vite';
import { AntDesignVueResolver } from 'unplugin-vue-components/resolvers';

export default defineConfig({
  plugins: [
    Components({
      resolvers: [
        AntDesignVueResolver({
          importStyle: false, // css in js
        }),
      ],
    }),
  ],
});
// tsconfig.app.json
{
  "files": ["./components.d.ts"],
}

```
