---
title: Jmeter系列-jmeter介绍
categories: 
- jmeter
tags: 
- jmeter学习笔记
- jmeter
- 性能压测
- jmeter执行顺序
---

## 1、简介
> 测试系统的负载能力和性能测试工具
> 纯Java开发
> 支持多种类型服务和协议
> 界面模式和命令行模式
> 扩展性强

## 2、安装和启动
**下载地址：https://jmeter.apache.org/download_jmeter.cgi**
**历史版本：https://archive.apache.org/dist/jmeter/binaries/**
**加压后双击运行 jmeter-3.3/bin/jmeter.bat 即可**

## 3、各元件介绍
jmeter有许多的元件，如图：
![](/assets/jmeter1.png)

**Jmeter元件主要包括：**
> 1.逻辑控制器（controller）
> 2.配置元件
> 3.定时器
> 4.前置处理器
> 5.采样器（Sampler）
> 6.后置处理器
> 7.断言
> 8.监听器

**Jmeter各元件优先级顺序：**
> 1.配置元件
> **2.前置处理器**
> **3.定时器**
> 4.采样器（Sampler）
> 5.后置处理器
> 6.断言
> 7.监听器

**Jmeter各元件是以控制器（controller）和采样器（sampler）为中心，其他元件都是围绕这两个元件进行逻辑处理。**
**采样器一般内嵌在逻辑控制器Controller中**


