---
title: 秒懂flex布局
categories: 
- css
tags: 
- flex布局
- css3
- css居中
---

## 1、背景
> 我们知道一切dom元素对css而言都是一个盒子，css正是以盒子模型为基础进行渲染CSSDom，而页面布局一直是css一个应用重点，那么如何通过css实现水平居中和垂直居中？
> 相信实现的方法有很多，如table，float等，但它们都有各自的缺陷。然而当flex出现后，一切都变得相当的简约而不简单，优雅而不优越，因为它也存在兼容性问题。
``` css
.centerContainer {
  display: flex;
  justify-content: center;
  align-items: center;
}
```
> 它的子元素就是水平和垂直居中，浏览器兼容性方面：IE10+  [***详情点击这里***](https://caniuse.com/#search=flex)

## 2、概念
flex由两个核心概念构成：容器和轴。容器包括父容器和子容器，轴分为主轴和交叉轴（即以横轴为主轴时，纵轴为交叉轴；以纵轴为主轴时，横轴为交叉轴；交叉轴=主轴逆时针90度）。flex属性一共有12个，父容器和子容器对应的属性各6个。

## 3、属性介绍
**父容器属性**
> 1) flex-direction (常用)
> 2) justify-content (常用)
> 3) align-items (常用)
> 4) align-content
> 5) flex-warp
> 6) flex-flow

**子容器属性**
> 1) align-self (常用)
> 2) flex (常用)
> 3) flex-grow
> 4) flex-shrink
> 5) flex-basis
> 6) order

### 3.1.常用属性介绍
#### 1) flex-direction
功能：flex-direction属性用于确定主轴的方向（等同于确定了交叉轴的方向）
使用：
``` css
.box {
  display: flex;
  flex-direction: row | row-reverse | column | column-reverse;
}
```
属性值 | 说明
--- | ---
row | 主轴为水平方向，起点在左端（默认值）
row-reverse | 主轴为水平方向，起点在右端
column | 主轴为垂直方向，起点在上
column-reverse | 主轴为垂直方向，起点在下

效果：（效果图作者：osimly）
![](/assets/css-flex-41.png)
![](/assets/css-flex-42.png)
![](/assets/css-flex-43.png)
![](/assets/css-flex-44.png)

#### 2) justify-content
功能：justify-content属性用于定义如何沿着主轴方向排列子容器（主轴）
使用：
``` css
.box {
  display: flex;
  justify-content: flex-start | flex-end | center | space-between | space-around;
}
```
属性值 | 说明
--- | ---
flex-start | 左对齐（默认值）
flex-end | 右对齐
center | 居中
space-between | 两端对齐，子容器之间间隔相等
space-around | 均匀分布，首尾两端的子容器到父容器的距离是子容器间距的一半

效果：（效果图作者：osimly）
![](/assets/css-flex-1.png)
![](/assets/css-flex-2.png)
![](/assets/css-flex-3.png)
![](/assets/css-flex-4.png)
![](/assets/css-flex-5.png)

#### 3) align-items
功能：align-items属性用于定义如何沿着交叉轴方向排列子容器（交叉轴）
使用：
``` css
.box {
  display: flex;
  align-items: flex-start | flex-end | center | baseline | stretch;
}
```
属性值 | 说明
--- | ---
flex-start | 起始端对齐（默认值）
flex-end | 末尾端对齐
center | 居中
baseline | 基线对齐，即默认指首行文字为基线
stretch | 拉伸对齐，子容器与父容器（高度/宽度）一致，相对于交叉轴方向而言

效果：（效果图作者：osimly）
![](/assets/css-flex-11.png)
![](/assets/css-flex-12.png)
![](/assets/css-flex-13.png)
![](/assets/css-flex-14.png)
![](/assets/css-flex-15.png)

#### 4) align-self
功能：align-self属性用于定义每个子容器自身如何沿着交叉轴方向排列（交叉轴），注意与align-items区别
> align-self属性与align-items属性一致，若是一个子容器同时被定义了align-items和align-self，以align-self属性值为准。

使用：
``` css
.box {
  display: flex;
  align-items: flex-start | flex-end | center | baseline | stretch;
}
```
属性值 | 说明
--- | ---
flex-start | 起始端对齐（默认值）
flex-end | 末尾端对齐
center | 居中
baseline | 基线对齐，即默认指首行文字为基线
stretch | 拉伸对齐，子容器与父容器（高度/宽度）一致，相对于交叉轴方向而言

效果：（效果图作者：osimly）
![](/assets/css-flex-31.png)
![](/assets/css-flex-32.png)
![](/assets/css-flex-33.png)
![](/assets/css-flex-34.png)
![](/assets/css-flex-35.png)
**align-self属性与align-items属性一致，若是一个子容器同时被定义了align-items和align-self，以align-self属性值为准**

#### 5) flex
功能：flex是一个复合属性，用于定义子容器自身的的伸缩比例，它是flex-grow,flex-shrink和flex-basis属性的缩写
使用：
``` css
.box {
  display: flex;
  flex: 0 1 auto;
}
```
属性值 | 说明
--- | ---
0 1 auto | flex-grow,flex-shrink和flex-basis属性的缩写，具体查看这三个属性的说明（默认值）
none | 0 0 auto （flex属性值允许1个或2个或3个属性的连用）

效果：（效果图作者：osimly）
![](/assets/css-flex-21.png)

常用的flex属性基本就这5个，掌握这5个属性之后，一般场景下的布局设置可以基本实现了。倘若需要满足其他特殊的布局，那么就有必要进一步了解其他7个属性了。

### 3.2.进阶属性介绍
#### 1) flex-wrap
功能：flex-wrap属性用于确定子容器是否需要换行
使用：
``` css
.box {
  display: flex;
  flex-wrap: nowrap | wrap | wrap-reverse;
}
```
属性值 | 说明
--- | ---
nowrap | 不换行（默认值）
wrap | 换行
wrap-reverse | 逆向换行

效果：（效果图作者：osimly）
![](/assets/css-flex-51.png)
![](/assets/css-flex-52.png)
![](/assets/css-flex-53.png)

#### 2) flex-flow
功能：flex-flow是一个复合属性，用于确定子容器的主轴方向和换行，是flex-direction和flex-wrap的组合
使用：
``` css
.box {
  display: flex;
  flex-flow: row nowrap;
}
```
属性值 | 说明
--- | ---
row nowrap | 左起水平方向主轴，且不换行（默认值）
row | 左起水平方向主轴。（flex的复合属性，都可以单独设置）

#### 3) align-content
功能：align-content属性用于确定子容器多行排列时，行与行之间的对齐方式（交叉轴），注意与align-items区别
> align-items针对单行子容器在交叉轴上的排列，而align-content则针对多行子容器在交叉轴上的排列方式

使用：
``` css
.box {
  display: flex;
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```
属性值 | 说明
--- | ---
flex-start | 起始端对齐（默认值）
flex-end | 末尾端对齐
center | 居中
space-around | 等边距均匀分布, 首尾两端的子容器到父容器的距离是子容器间距的一半
space-between | 等间距均匀分布, 子容器之间的间距相等
stretch | 拉伸对齐，多行子容器交叉轴方向长度相同，填满父容器

效果：（效果图作者：osimly）
![](/assets/css-flex-61.png)
![](/assets/css-flex-62.png)
![](/assets/css-flex-63.png)
![](/assets/css-flex-64.png)
![](/assets/css-flex-65.png)
![](/assets/css-flex-66.png)

#### 4) flex-basis
功能：flex-basis属性用于确定子容器自身在主轴方向的原始尺寸
使用：
``` css
.box {
  display: flex;
  flex-basis: 100px;
}
```
属性值 | 说明
--- | ---
100px | 子容器的原始宽度为100px（flex-direction默认为row，因此为宽度）

效果：（效果图作者：osimly）
![](/assets/css-flex-71.png)
![](/assets/css-flex-72.png)
结合flex-grow属性说明会更清晰一些

#### 5) flex-grow
功能：flex-grow属性用于确定子容器自身在主轴方向的扩展比例
使用：
``` css
.box1 {
  display: flex;
  flex-basis: 100px;
  flex-grow: 1;
}
.box2 {
  display: flex;
  flex-basis: 100px;
  flex-grow: 2;
}
```
属性值 | 说明
--- | ---
1 | 都是相对父容器而言，如上box1和box2为兄弟节点，父节点box宽度为480px，那么计算公式为：box.width = box1.width + box2.width = (120 + 1x) + (120 + 2x) = 480；即box1.width = 200px, box2.width = 280px;

效果：（效果图作者：osimly）
![](/assets/css-flex-81.png)
![](/assets/css-flex-82.png)

#### 6) flex-shrink
功能：flex-shrink属性用于确定子容器自身在主轴方向的收缩比例，注意与flex-grow的区别
> flex-grow属性是伸展，flex-shrink属性是收缩
使用：
``` css
.box1 {
  display: flex;
  flex-basis: 300px;
  flex-shrink: 1;
}
.box2 {
  display: flex;
  flex-basis: 600px;
  flex-shrink: 2;
}
```
属性值 | 说明
--- | ---
1 | 都是相对父容器而言，如上box1和box2为兄弟节点，父节点box宽度为480px，那么计算公式为：box.width = box1.width + box2.width = (480-x)+(480-2x) = 480；即box1.width = 320px, box2.width = 160px;

效果：（效果图作者：osimly）
![](/assets/css-flex-91.png)
![](/assets/css-flex-92.png)

#### 7) order
功能：order属性用于确定子容器自身在主轴方向的排列序号
使用：
``` css
.box1 {
  display: flex;
  order: 0;
}
```
属性值 | 说明
--- | ---
0 | 排列的序号，值越小越靠前，允许为负值（默认值）;

效果：（效果图作者：osimly）
![](/assets/css-flex-101.png)

## 4、总结
flex的每一个属性都是围绕着容器和轴两个概念而进行描述的。
flex有两个复合属性，复合属性允许设置单个属性和多个属性
align-self属性与align-items属性值相同，但两者设置的位置不同，前者在子容器中设置子容器自身属性，后者在父容器中设置子容器的属性，因此当同时设置子容器的属性时，以align-self的属性值为准
常用的属性如下：***flex-direction, justify-content, align-items, align-self, flex***
进阶的属性如下：***align-content, flex-warp, flex-flow, flex-grow, flex-shrink, flex-basis, order***
复合属性如下：***flex-flow, flex***


**参考**
***一劳永逸的搞定 flex 布局：https://juejin.im/post/58e3a5a0a0bb9f0069fc16bb***
***Flex 布局教程：语法篇：http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html***

