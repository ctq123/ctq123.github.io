---
title: 你不知道的javascript系列-深入理解相等比较类型转换
categories: 
- javascript
- 你不知道的javascript
tags: 
- 相等比较类型转换
- 不同类型转换
---

## 1、背景

## 2、类型转换规则
### 1.字符串与数字之间相等比较
**1）如果Type(x)是数字，Type(y)是字符串，则返回x == ToNumber(y)的结果**
**2）如果Type(x)是字符串，Type(y)是数字，则返回ToNumber(x) == y的结果**

### 2.其他类型与布尔类型之间相等比较
**1）如果Type(x)是布尔类型，则返回ToNumber(x) == y的结果**
**2）如果Type(y)是布尔类型，则返回x == ToNumber(y)的结果**

### 3.null与undefined之间相等比较
**1）如果x为null，y为undefined，则结果为true**
**2）如果x为undefined，y为null，则结果为true**

### 4.对象与非对象之间相等比较
**1）如果Type(x)是数字/字符串，Type(y)是对象，则返回x == ToPrimitive(y)的结果**
**2）如果Type(x)是对象，Type(y)是数字/字符串，则返回ToPrimitive(x) == y的结果**
> 对象（对象、函数、数组），非对象（数字、字符串、布尔值）
> 布尔值会先转换为数字

### 5.ToNumber()和ToPrimitive()
ToNumber对true转换为1，false转换为0，undefined转换为NaN，null转换为0
ToPrimitive抽象操作首先检查该值是否有valueOf()的返回值进行强制转换。如果没有就是使用toString()的返回值进行强制转换。
如果valueOf()和toString()均不返回基本类型值，会产生TypeError错误。



## 4、总结



## 5、参考
1.《你不知道的JavaScript》p43-57

