---
title: CSS - 层级异常情况
date: 2025-06-25 18:06:29
tags:
---


## 同级元素的z-index对比规则
1. 数值越大，层级越高
2. 数值相同，写在后面的层级越高
3. 数值为负，不参与层级比较
4. 同级比较，不跨父级
5. 默认值auto相当于z-index: 0，**但不等于设置z-index为0**


> z-index的对比仅限于同一父元素下的直接子元素‌。不同父元素下的子元素比较时，会先比较父元素的 z-index

# 但是

## 层叠上下文的形成条件
- 设置 z-index 值（非 auto）且 position 为 relative/absolute/fixed/sticky。
- 设置 opacity 小于 1、transform、filter 等属性。


```html
<html>
  <head>
    <style>
      #box {
        position: relative;
        width: 200px;
        height: 200px;
      }
      #d1,
      #d2,
      #d3 {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        display: flex;
        justify-content: center;
        align-items: center;
        z-index: 0;
      }
      #d1 {
        background-color: red;
        z-index: 5;
        opacity: 1;
      }
      #d2 {
        opacity: 0;
        background-color: green;
      }
      #d3 {
        opacity: 1;
        background-color: blue;
        top: 30px !important;
        left: 30px !important;
        opacity: 0.8;
      }
      #d4 {
        background-color: yellow;
        position: absolute;
        top: 50%;
        left: 50%;
        width: 100px;
        height: 100px;
        z-index: 6;
      }
    </style>
  </head>

  <body>
    <div id="box">
      <div id="d1"></div>
      <div id="d2"></div>
      <div id="d3">
        <div id="d4"></div>
      </div>
    </div>
  </body>
</html>
```

#d3未设置z-index并且postion为absolute，#d4会上升到父级层叠上下文，会与#d1比较

如何给#d3设置z-index、opacity小于1、tranform、filter，那么就不会上升 继续遵循同级比较的规则