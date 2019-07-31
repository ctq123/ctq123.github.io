---
title: 深入理解Web Worker
categories: 
- javascript
tags: 
- web worker
- js多线程
---

## 1.背景
javascript采用的是单线程模型，随着时代的进步，单线程编程已无法充分发挥多核CPU计算机的计算能力，而Web Worker的出现为javascript创造了多线程编程，为未来javascript的发展提供另外一种新思路，这并不是说javascript未来会将单线程模型改为多线程模型，因为涉及到一个核心问题，假设javascript有两个线程，一个线程要添加dom节点，另外一个线程要删除这个节点，那浏览器引擎要以哪个为准呢？因此单线程模型将来也不会改变。Web Worker的出现只是为javascript提供了一种多线程编程模式，但并未改变javascript核心本质单线程模型。因为Web Worker本身有很多的限制，不允许操作DOM，且受主线程的控制。那么什么是“主线程”？

用于执行javascript和更新用户界面的进程通常被称为“浏览器UI线程”（尽管对所有浏览器来说，称为“线程”不一定准确, 以下我们将其简称为“主线程”）。通常情况下，执行javascript和更新用户界面是交替进行的，倘若执行javascript时间过长，势必会出现界面“假死”现象。为了更优雅地解决这种情况，Web Worker应运而生。

## 2.基础部分
### 2.1 web worker特性
> ***web worker有自己的特性，有些属性名称与window中的属性名称相同，但其功能只是后者特性的一个子集。***
> ***由于web worker线程的设计理念就是为减轻主线程计算方面问题，因此跟UI渲染相关对象不能也不应该读取。***

#### 2.1.1 web worker重要特性

1）navigator对象（只有四个属性appName、appVersion、userAgent、platform）

2）location对象（属性与window.location相同，但所有属性都是只读）

3）self对象（指向worker线程用例自身）

4）XMLHttpRequest对象

5）setTimeout/setInterval方法

6）importScript()方法

7）close()方法

8）所有ECMAScript对象，如Object/Array/Date等


#### 2.1.2 web worker不能读取特性

1）DOM对象

2）window对象

3）document对象

### 2.2 基本用法
#### 2.2.1 web worker 的API
主线程属性 | 属性名称
--- | ---
Worker.onmessage | 接收消息函数
Worker.postMessage | 向worker线程发送消息函数
Worker.onerror | worker线程错误监听函数
Worker.onmessageerror | 接收消息错误监听函数（发送数据无法序列化成字符串时触发）
Worker.terminate | 关闭worker线程函数

worker线程属性 | 属性名称
--- | ---
self.onmessage | 接收消息函数
self.postMessage | 向主线程发送消息函数
self.onmessageerror | 接收消息错误监听函数（发送数据无法序列化成字符串时触发）
self.importScripts | 加载外部js函数
self.close | 关闭worker线程函数


#### 2.2.2 主线程创建worker
首先让我们看看如何创建worker线程：
```js
const myworker = new Worker(jsURL, options)
```
参数说明：
> jsURL: 必选，worker将执行的脚本的url（必须同源）
> options: 可选，该对象包括{type, credentials, name}属性
例子如下：
```js
// main.js
var myworker = new Worker('my_worker.js')

var myworker = new Worker('my_worker.js', {name: 'my_worker', type: 'listen_status'})
```

#### 2.2.3 主线程发送数据
主线程向子线程发送一个“Jack”字符串数据：
```js
// main.js
myworker.postMessage('Jack')
```

#### 2.2.4 主线程接收数据
主线程接收子线程返回的数据，并打印出来：
```js
// main.js
myworker.onmessage = function(event) {
  console.log(event.data)
  ...
}
```

#### 2.2.5 worker线程接收数据
子线程接收主线程的数据并打印：
```js
// my_worker.js
self.onmessage = function(event) {// self指向my_worker线程当前用例
  console.log(event.data)
  ...
}
```

#### 2.2.6 worker线程发送数据
子线程向主线程发送一个“hello”字符串的数据：
```js
// my_worker.js
self.postMessage('hello')
```

#### 2.2.7 终止worker
若我们想要关闭子线程，我们可以在主线程中直接关闭，方法为：
```js
// main.js
myworker.terminate()
```
我们也可以在子线程中直接关闭，通常在子线程任务结束后关闭，方法为：
```js
// my_worker.js
self.close()
```

#### 2.2.8 错误处理
主线程可以监听worker线程是否发生错误，并做出相应的处理
```js
// main.js
myworker.onerror(function(e) {
  var line = e.lineno // 错误文件行号
  var filename = e.filename // 错误文件名
  var msg = e.message // 错误内容
  console.error('line:'+line+' in '+filename+': '+msg)
})
```

#### 2.2.9 加载外部文件
子线程可以通过importScripts()方法引入其他脚本，接收0个或者多个url作为参数来引入资源
```js
// my_worker.js
importScripts() // 什么都不引入
importScripts('file1.js') // 加载一个文件
importScripts('file1.js', 'file2.js') // 加载两个文件 
```

#### 2.2.10 完整小用例
下面我们通过看一个完整的小用例，看看主线程与子线程的交互过程：
```js
// main.js
if (window.Worker) {
  var myworker = new Worker('my_worker.js')
  myworker.postMessage('Jack')
  myworker.onmessage = function(event) {
    console.log(event.data) // hello, Jack
  }
}
```

```js
// my_worker.js
self.onmessage = function(event) {
  console.log(event.data) // Jack
  self.postMessage('hello, '+ event.data)
  self.close()
}
```

注意：
> 上述以on开头的函数是一个回调函数，都可以使用addEventListener替换，如slef.onmessage(function)等同于self.addEventListener('message', function)。如果你细心会发现一件很有趣的事情，在javascript衍生的技术或语言，它们的设计者都遵循这样一种规则：所有以on开头的函数都可以作为一种回调函数。
> 子线程中其实也可以嵌套创建另外一个子线程，可以一直嵌套创建下去，但对应的逻辑就会变得复杂，不建议这样做。

至此，Web Worker基础使用方法已经介绍完毕。当然，我们知其然，应当知其所以然。


## 3.进阶部分
事实上，Web Worker定义了两类工作线程：**专用线程和共享线程**。上述介绍的是专用线程。专用worker只能在一个页面所使用，而共享worker可以被多个页面所共享。创建共享worker的代码如下：
```js
var shareworker = new SharedWorker('share_worker.js')
```
共享worker和专用worker它们的API基本上是一致的，除了名称不同以外，其实它们之间的最大区别在于它们的数据通信方式。共享worker通过端口port连接进行通信，在通信之前必须打开该端口，打开方法为:
```js
// main.js
shareworker.port.start()
shareworker.port.onmessage = function(e){}
shareworker.port.postMessage()

// share_worker.js
self.port.start()
self.port.onmessage = function(e){}
self.port.postMessage()
```
如果主线程与共享线程之间需要双向通信，那么它们都需要调用start()方法。同时，使用API接口方法时也需要通过port来调用，如上述代码例子，这里就不一一展开说明了。

下面将进一步解析主线程与worker线程并行执行的关系流程，主线程与worker线程的数据通信方式，以及worker线程的实际应用场景。

### 3.1 主线程与worker线程
下面看看主线程与子线程运行时的关系图：
![](/assets/js-web-worker-1.png)

如上图，javascript主线程执行过程中，异步创建worker线程，然后主线程将继续执行其他任务。倘若worker线程还没有创建完成，主线程就直接通过worker.postMessage发送消息给Worker线程，该消息会进入一个临时消息队列中，等worker线程创建成功后，worker线程会从临时消息队列中接收消息，并处理。等处理完成后，通过self.postMessage将消息发送会主线程中。主线程接收到消息后，等待主线程处理完其他任务片段后，才会继续执行worker.onmessage中的回调函数。

在worker线程创建完成的前提下，主线程再次发送消息给worker线程时，worker线程会直接收到消息，而不必经过临时消息队列，如上图对应的交互过程1和2。

最后，当任务结束后，主线程和worker线程都可以关闭worker线程，主线程通过worker.terminate关闭，worker线程通过调用自身的self.close()关闭。

> ***主线程和worker线程发送消息和接收消息流程基本相同，然而事实上，由于主线程受到内核任务调度的影响，worker线程发送的消息，主线程未必会收到消息就会马上往下执行，需要等待前面的任务执行完成才会继续往下执行；而主线程正确发出消息后，worker线程一般会收到消息就会继续往下执行。***

### 3.2 数据通讯方式
主线程与worker线程数据通信中传递的内容一般有两种，一种是传递原始值（字符串，数字，布尔值，null和undefined），另一种是传递object和Array的实例，其他的类型就不可以了。

当传递的内容为原始值时，传递的内容将被串行化，然后将串行化的字符串发送给worker线程，然后再还原，反过来亦是如此；

当传递的内容是对象或文件时，传递的内容将进行二进制转换，然后将二进制数据发送给worker线程。

其实若是了解java的同学，可以将这一过程理解为序列化和反序列化的过程。

由此可知，**主线程与worker线程之间的通信数据，是一种拷贝关系，即值传递，而不是传址。worker线程改变传递进来的数据并不会影响到主线程。**

既然是一种值传递的拷贝关系，倘若传递的数据很大，比如100M的文件（转化成二进制数据），默认情况下浏览器会生成一个原文件的拷贝，就会引起很大的性能问题。

为了解决这个问题，worker线程提供了一种方法叫做Transferable Objects，允许主线程把二进制数据直接转移给worker线程，但一旦转移，主线程将无法使用这些二进制数据。这对处理影音文件，3D运算，图片处理等意义重大，方法如下：
```js
// Transferable Objects语法
worker.postMessage(arrayBuffer, [arrayBuffer])

// 示例
var arrbuf = new ArrayBuffer(1);
worker.postMessage(arrbuf, [arrbuf])
```

### 3.3 实际应用场景
Web Worker适用于处理纯数据，或者与浏览器UI无关的长时间运行脚本。

那么我们如何定义日常UI响应为长时间呢？多久才算“长时间”，300毫秒，500毫秒，还是1秒？

事实上，据国外专家研究，单个JavaScript操作花费的总时间不应该超过100毫秒。当然不包括文件上传和下载这种操作，这里针对的只是一般的UI响应场景

考虑这样一个例子：解析一个很大的JSON字符串，假设其数据量很大至少需要5000毫秒才能完成解析。很明显，它远远超出客户端允许javascript运行的时间，将严重影响用户体验。这种场景下，worker线程则成为最理想的解决方案。代码如下：
```js
// main.js
if (window.Worker) {
  var worker = new Worker('my_worker.js');
  worker.onmessage = function(e) {
    var jsonData = e.data;
    // 业务中处理json数据的函数
    doSomething(jsonData);
  }
  worker.postMessage(jsonStr);
  
  // 业务中处理json数据的函数
  function doSomething(jsonData) {
    // ……
  }
}
```
```js
// my_worker.js
self.onmessage = function(e) {
  var jsonStr = e.data;
  var jsonData = JSON.parse(jsonStr);
  self.postMessage(jsonData);
}
```
解析一个大字符串只是web worker其中一个应用场景，典型的应用场景如下：
> **1）编码/解码大字符串**
> **2）复杂的数学运算（包括图像或视频处理）**
> **3）大数组排序**

事实上，**任何超过100毫秒的处理过程，都应该考虑Worker线程方案处理，当然前提是浏览器支持Web Workers**。

## 4.总结
由上所述，Web Worker的出现，充分发挥了多核CPU计算能力，解决浏览器单线程中高负载计算的问题，提升了用户体验。由浏览器的渲染原理可知，浏览器的主线程任务包括两部分：渲染任务和js解析任务，而Web Worker只是js解析任务中的一部分，处理计算相关的任务，因此它有自己的特性，不可以直接调用window中的绝大部分对象，dom对象以及document对象。

## 5.参考
1.【MDN文档Web Worker API介绍】https://developer.mozilla.org/zh-CN/docs/Web/API/Worker/Worker
2.【MDN文档Web Worker API使用】https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Using_web_workers
3.【高性能JavaScript】第6章 快速响应的用户界面
4.【阮一峰的Web Worker 使用教程】http://www.ruanyifeng.com/blog/2018/07/web-worker.html
5.【腾讯AlloyTeam 深入理解Web Worker】http://www.alloyteam.com/2015/11/deep-in-web-worker/

