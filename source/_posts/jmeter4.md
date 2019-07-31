---
title: Jmeter系列-使用Jmeter做阶梯式加压
categories: 
- jmeter
tags: 
- jmeter学习笔记
- jmeter
- 性能压测
- 阶梯式加压
---

## 1、背景
有些场景，需要我们使用阶梯式加压，慢慢一批一批数量，逐渐往上加压，最高并发运行线程不能因为运行出错而减少
虽然普通Thread Group也可以进行阶梯式加压，但由上一篇《[Jmeter线程组内各请求的执行顺序](https://ctq123.github.io/2019/01/24/jmeter2/)》我们可以知道，若线程组启动时间参数设置不当，会造成线程组的加压方式并不会按照我们预想地"阶梯式加压"，有可能第一批线程还没跑完，就会先跑第二批线程。
注意这里介绍的阶梯式加压，与普通Thread Group加压是有一些区别，具体后面会有介绍。

## 2、阶梯式加压
Jmeter提供两种方式可以实现批量逐步往上加的效果，一是Stepping Thread Group，二是Concurrency Thread Group

下载方式：

Jmeter Plugins Manager中下载Custom Thread Groups

如果选项中没有Plugins Manager（如下图），请自行下载，地址：https://jmeter-plugins.org/install/Install/

![](/assets/jmeter4-1.png)

使用Concurrency Thread Group,如图
![](/assets/jmeter4-2.png)

Concurrency Thread Group比Stepping Thread Group优点：
> 1）并发线程组允许控制测试的长度
> 2）线程在进程中间结束的情况下创建替换线程，节省内存（压测机器）

参数说明：
- Target Concurrency: 并发线程数
- Ramp Up Time: 加速时间
- Ramp-Up Steps Count: 加速步奏计数
- Hold Target Rate Time: 保持目标速率运行时间
- Time Unnit: 时间单位（分或秒）
- Thread Iterations Limit：线程迭代次数限制
- Log Threads Status into File: 将线程状态记录到日志文件（线程启动和停止事件保存为日志文件）

举个栗子：
Target Concurrency：10（A）
Ramp Up Time： 10（B）
Ramp-Up Steps Count: 10（C）
Hold Target Rate Time: 1
Time Unit: seconds
（程序组内每个线程启动的定时器设置为1s）

由上图可以看出，每隔1s（10(C)/10(A)）启动一个线程。程序内部设置定时器也应该为1s，否则线程启动数量不可控

再达到目标线程10个，总的启动线程数量为：1+2+3+....+10 = 10*(10+1)/2=55
持续10个线程运行时间为1s，数量为：10*1=10（同理若持续为5s，则为10 * 5=50）
总的线程数量为：55+10=65

**注意：最终运行线程数量运算较复杂，非必要场景下，不推荐使用**

## 3、总结

### Concurrency Thread Group与普通 Thread Group区别：

**1.最大的区别就是假设目标线程为100，运行过程中若有线程出错，普通线程组最终运行的目标线程会小于100，而Concurrency Thread Group最终运行的目标线程始终会保持100**

**2.普通线程组若想查看其中错误的线程出错，根据日志是很难查找得到的（当目标线程很大的情况下），而Concurrency Thread Group可以将错误的线程保存在日志文件中，更容易得到错误线程的报错信息**



