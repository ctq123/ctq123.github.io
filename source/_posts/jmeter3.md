---
title: Jmeter系列-Jmeter登录session共享
categories: 
- jmeter
tags: 
- jmeter学习笔记
- jmeter
- 性能压测
- session共享
---

## 1、背景
有很多情况，我测试的接口或场景与登录操作无关，而又必须先登录才拥有对应的接口权限，测试对应的接口。那么有哪些共享session的方法？

## 2、共享登录session方式
### 2.1、利用仅一次控制器共享session（单个线程组）
利用简单的仅一次控制器，处理登录操作，如下图
![](/assets/jmeter3-1.png)

提取登录请求的session，然后将其保存在一个变量中即可。

然而，上述方式只适用于在当前线程组内session共享，若需要跨线程组共享一个session，那就需要使用property属性。

### 2.2、利用property属性共享session（多个线程组）
利用Jmeter本身提供的property属性，垮线程组的session共享，使用${ __setProperty(,,) }保存，如图
![](/assets/jmeter3-2.png)

然后在要用的地方，使用${ __property() }提取， 如图
![](/assets/jmeter3-3.png)

## 3、总结
**仅一次控制器适用于单线程组内共享session，property属性适用于多个线程组共享session**



