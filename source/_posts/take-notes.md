---
title: 随手笔记
categories: 
- javascript
tags: 
- let var const
- canvas svg vml
- js时间戳
---

## 1.图表绘制底层Canvas, SVG, VML的对比，各适用场景
1.性能对比：Canvas的性能受画布尺寸大小影响更大，SVG的性能受图形元素个数影响更大；
2.适用场景：
2-1）Canvas可以轻松应对大量数据和动态特效展现的场景；
2-2）SVG可以在移动端展示，使其不再为内存而担忧；
2-3）VML可以兼容低版本IE；

## 2.let和var区别
1.let定义了块级作用域，只在代码块内有效；
2.let在同一作用域内，不允许重复声明同一个变量，但允许在垮作用域声明同一个变量；
3.ES5只有全局作用域和函数作用域：
> 全局作用域，局部作用域会覆盖全局作用域
> 函数作用域，用来计数的循环变量会泄露为全局变量
```js
for (var i = 0; i < 10; i++) {
  // ...
}
console.log(i)// 10
for (let i = 0; i < 10; i++) {
  // ...
}
console.log(i)// 报错
```
***此外，for的每一次循环相当于一个新的块级作用域，var是不断覆盖老的变量（在整个for循环体内有效），而let则是声明新的变量（只在本轮循环有效）***

## 3.const变量
1.const声明一个常量，它实际保证的并非是它的值不变，而是保证变量指向那个内存地址所保存的数据不得改动（对简单类型如数值，字符串，布尔值好理解，但对复合型如对象和数组，则需要理解指针地址的概念）
2.const声明一个常量必须立刻初始化赋值；
3.const作用域与let作用域相同；

## 4.js获取时间戳方法
1) var cur = Date.parse(new Date());
2) var cur = (new Date()).valueOf();
3) var cur = new Date().getTime();
4) var cur = Date.now();
