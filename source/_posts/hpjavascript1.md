---
title: 高性能JavaScript系列-无阻塞加载js文件
categories: 
- javascript
tags: 
- 高性能JavaScript
- 无堵塞加载
---


代码1
```
<script type="text/javascript" src="file1.js"></script>
```
代码2

```
var script = document.createElement("script");
script.type = "text/javascript";
script.src = "file1.js"
document.getElementsByTagName("head")[0].appendChild(script);
```
代码1与代码2的异同：
- 代码1在执行过程中，文件下载后会立刻执行，堵塞页面的其他进程（如其他下载进程）
- 代码2在执行过程中，文件下载和执行过程不会堵塞页面的其他进程，直到DOM加载完成，因此该方法可以与其他资源并行下载

---

有多种无堵塞下载JavaScript的方法：

**1. 使用script标签的defer属性**

**2. 使用动态创建的script元素来下载并执行代码**

**3. 使用XHR对象下载JavaScript代码并注入页面中**

以上三种方法各有缺点：

> 1-1) 支持的浏览器较少

> 2-1) IE内核浏览器需要额外处理

> 3-1) 不支持CDN下载

