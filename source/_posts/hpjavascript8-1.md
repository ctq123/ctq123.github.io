---
title: 高性能JavaScript读书笔记
categories: 
- javascript
tags: 
- 高性能JavaScript
---

## 1、js如何将十进制的数字转换成二进制？
使用toString(2)进行转换即可，例如：
```js
var num = 8;
console.log(num.toString(2)); // "1000" 
```

## 2、如何获取当前页面有多少个dom节点数？
```js
console.log(document.getElementsByTagName('*').length); // "1000" 
```

## 3、js打印函数方法运行时长
```js
// 启动计时器
console.time('testForTime');
// 运行程序
……
// 停止计时
console.timeEnd('testForTime')
// 4343.123ms
```
