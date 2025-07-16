---
title: Electron
date: 2025-07-15 10:46:44
tags:
---

## 使用脚手架创建vue、vite项目
[electron-vite](https://github.com/alex8088/electron-vite)


#### 隐藏titlebar
```js
// src/main/index.ts
const mainWindow = new BrowserWindow({
    titleBarStyle: 'hidden'
})
```
给元素添加样式使其可拖拽
```css
.dragable {
    -webkit-app-region: drag;
}
```

# electron-vite + better-sqlite3
1. 明显版本不一致的问题按如下修复
```bash
npm i -D @electron/rebuild
# package.json
"rebuild": "electron-rebuild -f -w better-sqlite3"
```
2. Error: Could not dynamically require "xxx/electron-app/build/better_sqlite3.node". Please configure the dynamicRequireTargets or/and ignoreDynamicRequires option of @rollup/plugin-commonjs appropriately for this require call to work.

此错误只需要将package.json中的better-sqlite3移动到dependencies即可。