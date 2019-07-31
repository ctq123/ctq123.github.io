---
title: 随手笔记
categories: 
- javascript
tags: 
- let var const
- canvas svg vml
- js时间戳
- 高性能javascript
- 你不知道的javascript
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
5) var cur = +new Date();

## 5.匿名函数的递归arguments.callee
```js
var i = 0;
(function(){
  console.log(i++)
  i === 60 && i = 0;
  arguments.callee();
})(); // 不断打印0-59的死循环
```
但是由于arguments.callee每次递归时需要重新创建，浪费内存资源，现已不推荐使用。

## 6.this的绑定规则及其优先级
1）由new调用？绑定到新创建的对象
2）由call或者bind(或者apply)调用？绑定到指定的对象（显式绑定）
3）由上下文对象调用?绑定到那个上下文对象（隐式绑定）
4）默认：在严格模式下绑定到undefined,否则绑定到全局对象

ES6中的箭头函数并不会使用四条标准的绑定规则，而是根据当前词法作用域来决定this,具体来说，箭头函数会继承上一层函数调用的this绑定（无论this绑定到什么）。这其实与代码中的self=this机制是一样的。

摘自《你不知道的javascript》

## 7.创建对象的方法
1）{}
2）new Object()
3) Object.create(null)

如果要访问对象中的一个属性，引擎实际上会先调用内部默认的[[Get]]操作，如果没有找到，就会通过[[prototype]]链来查找。

A和B的委托关系：
B = Object.create(A)

## 8.prototype机制
[[Prototype]]机制就是指对象中的一个内部链接引用另一个对象。
本质就是对象之间的关联关系。

## 9.javascript的原类型
有7大内置类型：
1) null
2) undefined
3) boolean
4) number
5) string
6) object
7) symbol（ES6新增）
除了对象之外，其他的称为基本类型。
function，array，Date为什么不是呢？这些其实都是object的一个子类型
> javascript中变量是没有类型的，只有值才有。变量可以随时持有任何类型的值。

## 10.数组
1）array是一个object的一个子类型，数组中元素可包含任何类型
2）数组可以通关过数字检索，也可以通关字符串检索（不会计算在数组长度内），如：
```js
var a=[];
a[0]=1;
a['foor']='aa';
// a.length=1

var b=[];
b['13']='hahha';// 注意数字会转换
// b.length=14
// b[0]=undefined // 建议不要创建这种空数组，也就是不要使用这种创建方式
```

## 11、JSON.stringify()
在转换成json字符串的过程中，遇到undefined,function和symbol会自动将其忽略，在数组中会直接返回null
```js
JSON.stringify([1, undefined, function(){}, 'a'])
// "[1, null, null, 'a']"
```

## 12、==与===区别
==和===都是比较值，前者不需要比较类型，后者还需要比较类型
> 本质上 == 允许在相等比较中进行强制类型转换，而===不允许
> == 是先在不同类型之间进行转换，然后再进行比较。而不同类型之间的转换规则这里不展开描述

## 13、数字的取值
```js
if (a==0 && a==1) {
  console.log('miracle')
}
```
上述代码会打印'miracle'吗？如会，在什么场景下会发生？
答案会。只要数字每次取值，对其进行串改即可，如下：
```js
function myNumber(n) {
  this.n = n
}
myNumber.prototype.valueOf = function () {
  return this.n++
}

var a=new myNumber(0)
// a初始为0，以后a每一次取值都会递增一次
// 注意a并不是数字Number类型，若要===比较，需要直接修改Number.prototype.valueOf，但这是危险操作，慎用！慎用！
// 其实这个命题本身就是坑爹，一般实际场景不会用到这个诡异的逻辑
```

## 14、布尔类型与其他类型等值比较的坑
```js
var a='42'
var b=true
a==b // false

var c='42'
var d=false
c==d // false
```
一般而言，一个值不应该是非真必假吗？这里怎么出了一个太监，既非真亦非假！？

根据ES5规范，当布尔类型x与其他基本类型y比较时：
> 1）如果Type(x)是布尔类型，则返回ToNumber(x) == y的结果；
> 2）如果Type(y)是布尔类型，则返回x == ToNumber(y)的结果；

上述的结果分析过程如下：
a='42' => a=42
b=true => b=1
a==b => 42==1 // false

c='42' => c=42
d=false => d=0
c==d => 42==0 // false

根本原因是布尔类型的比较，并没有发生布尔值的比较和强制类型转换。

**个人建议无论什么情况下都不要使用==true和==false**

**注意这里只是说==，并不是===，因为===不允许强制类型转换**

类似这样的太监还有很多，比如NaN != NaN, [] == ![]。是不是有点颠覆思维？

摘自《你不知道的javascript》

## 15.箭头=>替代function函数
=>是关于this、arguments、super的词法绑定。
1）改变this词法作用域
2）箭头函数没有自己的arguments

## 16.原型模式
```js
function Person() {
  //
}
Person.prototype.name = "haha"
var p1 = new Person()
var p2 = new Person()
p1.name = 'kaka'
console.log(p2.name) // "kaka", 而不是“haha”
```
原型模式最大缺点是所有的数据都共享。
动态原型模式，寄生构造函数模式，寄生组合式继承
摘自《js高级程序设计》

## 17.垃圾回收机制
有两种方式：标记清除和引用计数
1）标记清除
进入执行环境上下文时对变量进行标记，退出执行环境上下文时对变量进行清除
是目前最为常用的方法

2）引用计数
当一个值赋予一个变量时，该值对应的引用进行加1；当该值对应的变量赋予其他值时，值对应的引用减1
目前较为少用，因为它存在一个严重的缺陷：循环引用时无法回收垃圾，严重时会造成浏览器崩溃，因此在写代码时最好在适当时机进行赋值为null来解除引用

> 由于浏览器每隔一段时间会自动回收垃圾，如果需要立刻回收垃圾IE浏览器可以通过window.CollectGarbage()方法进行回收，类似于java的System.gc()

摘自《js高级程序设计》

## 18.preventDefault()和stopPropagation()区别
1）preventDefault()
阻止浏览器的默认行为：对于一些特定事件，浏览器有其默认行为，比如点击链接会转跳，填表单回车会提交等

2）stopPropation()
阻止事件冒泡：点击当前节点不会触发父节点的点击事件

垮浏览器处理两个方法的兼容性代码如下：
```js
var EventUtil = {
  preventDefault: function (e) {
    if (e.preventDefault) {
      e.preventDefault()
    } else {
      e.returnValue = false
    }
  },
  stopPropagation: function (e) {
    if (e.stopPropagation) {
      e.stopPropagation()
    } else {
      e.cancelBubble = true
    }
  }
}
```
## 19.setTimeout和setInterval最小时间间隔
H5标准规定：
1）setTimeout的最小时间间隔是4ms
2）setInterval的最小时间间隔是10ms
但不同的浏览器会有不同的处理，如firefox对setTimeout的最小时间间隔为10ms
对不处在当前窗口的页面，浏览器会将时间间隔扩大到1000ms
在nodejs中setTimeout最小时间间隔是1ms

## 20.函数防抖动和函数节流
函数防抖动（debounce）：函数会在被触发的n秒之后去执行，如果n秒之内又被触发，则重新计时
函数节流（throttle）：规定在单位时间之内触发多次函数，只有一次生效，单位时间内只会触发一次
相同点：都是防止某一事件段内频繁触发
区别：防抖动是某一段时间内只会执行一次，节流则是单位时间内只会触发一次，详情看代码说明

```js
// 防抖动
// 5秒内不断触发，时间间隔2秒，结果触发1次
let timer
function debounce(func, delay) {
  return function () {
    clearTimeout(timer)
    timer = setTimeout(() => {
      func.apply(this, arguments)
    }, delay)
  }
}

// 节流
// 5秒内不断触发，时间间隔2秒，结果触发3次
let last, timer
function throttle(func, delay) {
  return function () {
    const now = Date.now()
    if (timer) {
      clearTimeout(timer)
    }
    if (last && now - last < delay) {// 这里是处理最后4-5秒内那一次的情况
      timer = setTimeout(() => {
        last = now
        func.apply(this, arguments)
      }, delay)
    } else {// 处理0-2，2-4内的情况
      last = now
      func.apply(this, arguments)
    }
  }
}
```
应用场景：
1）防抖动：
用户输入及时搜索时，用防抖动节约请求资源
调整窗口大小window resize时，用防抖动让其只触发一次

2）节流
鼠标不断点击触发，mosedown事件
监听滚动事件，滑到底部自动加载更多


## 21.bind, call和apply异同
相同点：都是改变this的指向
不同点：
1）bind与call，apply的区别是bind绑定第一个参数后，不会马上执行回调函数，而是返回一个新函数，然后再由新函数执行剩余的参数，有点类似柯里化
2）call与apply绑定第一个参数会马上回调函数，它们的区别是apply参数数个数组，call的参数是数组列表的元素


