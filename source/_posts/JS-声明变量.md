---
title: JS - 声明变量
date: 2025-07-01 10:01:15
tags: ['JS', '变量']
---

> 在ES6出现之前，JavaScript中声明变量就只有通过 **var 关键字**，函数声明是通过**function 关键字**，而在ES6之后，声明的方式有 var、let、const、function、class ，本文主要讨论var、let和const之间的区别。

*   var：变量提升（无论声明在何处，都会被提至其所在作用域的顶部）
*   function：变量提升
*   let：无变量提升（未到let声明时，是无法访问该变量的）
*   const：无变量提升，声明一个基本类型的时候为常量，不可修改；声明对象可以修改
*   class：无变量提升

1.  var 声明的变量属于函数作用域，let 和 const 声明的变量属于块级作用域；
2.  var 存在变量提升现象，而 let 和 const 没有此类现象；
3.  var 变量可以重复声明，而在同一个块级作用域，let 变量不能重新声明，const 变量不能修改。


```js
// 暂时性死区
var tmp = 123;
if (true) {
    tmp = 'abc'; // ReferenceError
    let tmp;
}
```

> 上面代码中，存在全局变量tmp，但是块级作用域内let又声明了一个局部变量tmp，导致后者绑定这个块级作用域，所以在let声明变量前，对tmp赋值会报错。
```js
var a = [];
for (var i = 0; i < 10; i++) {
    a[i] = function () {
        console.log(i);
    };
}
a[6](); // 10
```

> 上面代码中，变量i是var命令声明的，在全局范围内都有效，所以全局只有一个变量i。
```js
for(var i=0;i<5;i++){       
    setTimeout(function() {
        console.log(i);
    }, 0)
}
// 结果 5 5 5 5 5
for(let i=0;i<5;i++){
    setTimeout(function() {
        console.log(i);
    }, 0)
}
// 结果 0 1 2 3 4
```

> for循环属于同步，setTimeout属于异步，同步代码执行完再去执行异步，也就是上面每次循环都会向任务队列里加一个定时器，等for循环结束后开始执行，var共享一个变量，let因为块级作用域的原因，打印的是正常的。
```js
    test();
    var test = () => console.log(1)
    test();
    function test() {console.log(2)}
    test();
    // 2 1 1
```
> function声明变量提升

