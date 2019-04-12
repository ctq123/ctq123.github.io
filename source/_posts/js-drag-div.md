---
title: js实现鼠标可拖拽的div
categories: 
- javascript
tags: 
- 可拖拽div
---

## 1、背景
最近公司项目的一项功能，是基于一个可拖拽的div基础上实现其业务逻辑的。经过探索，有两种实现方式，分别是基于mousedown和drag技术实现的。下面把遇到的一些坑记录下来，希望对小伙伴们有帮助^-^
> 下面页面代码是react代码，但实现的思想是一致的。

## 2、基于mousedown技术
这种技术是目前网络中较为“流行”的技术，意思就是随便google一下，网络上就会有一大堆，然而这种技术在某些应用场景下会存在一些不可避免的缺陷，下面会讲到。

事件流程：
> 1）onmousedown：触发拖拽
> 2）onmousemove：实现拖拽
> 3）onmouseup：清除拖拽监听


```jsx
class TestTab extends Component {
  constructor (props) {
    super(props)
    this.state = {
      x: 0,
      y: 0
    }
  }

  handleOnMouseDown (e) {
    e.preventDefault()
    
    document.onmousemove = (ev) => {
      ev.preventDefault()
      this.setState({
        x: ev.clientX,
        y: ev.clientY
      })
    }
    document.onmouseup = (ev) => {
      ev.preventDefault()
      document.onmousemove = null
      document.onmouseup = null
    }
  }

  render () {
    const { x, y } = this.state

    return (
      <div className='parent' style={{ left: x, top: y }} onMouseDown={e => this.handleOnMouseDown(e)}>
        <div className='child' style={{ 'height': '200px', 'width': '300px', 'border': '1px solid red' }}>
        </div>
      </div>
    )
  }
}
```
上述便可实现最简单的拖拽效果

然而，上面会出现这样一个问题，正常业务中，拖拽往往只能限制在一定地区域内拖拽，比如不能拖拽出window窗口(调试模式下)，不能拖出某个区域。

```jsx
class TestTab extends Component {
  constructor (props) {
    super(props)
    this.lx = 5
    this.rx = 60
    this.ty = 50
    this.state = {
      x: 10,
      y: 60,
    }
  }

  handleOnMouseDown (e) {
    //……
    document.onmousemove = (ev) => {
      //……
      let cx = ev.clientX
      let cy = ev.clientY
      const dx = Math.max(0, this.maxX - this.rx)
      const dy = Math.max(0, this.maxY - this.ty)
      cx = Math.max(cx, this.lx)
      cy = Math.max(cy, this.ty)
      cx = Math.min(cx, dx)
      cy = Math.min(cy, dy)
      this.setState({
        x: cx,
        y: cy
      })
    }
    document.onmouseup = (ev) => {
      //……
    }
  }

  showTip (e) {
    // console.log(e.clientX, e.clientY)
  }

  render () {
    const { x, y } = this.state
    const ww = window.innerWidth || window.clientWidth
    const wh = window.innerHeight || window.clientHeight
    this.maxX = ww
    this.maxY = wh

    return (
      <div className='parent' style={{ left: x, top: y }} onMouseDown={e => this.handleOnMouseDown(e)}>
        <div className='child' style={{ 'height': '200px', 'width': '300px', 'border': '1px solid red' }} onClick={e => this.showTip(e)}>
        </div>
      </div>
    )
  }
}
```
上述限制其不能拖拽超过某个区域（具体区域根据自己的业务场景进行设定）

然而实际业务过程中我们会在class为parent或者child的div层进行各种点击事件，这样就会出现这样一个问题，触发父元素parent拖拽动作onMouseDown的同时，也会触发子元素child的onClick事件，这并不是我们希望看到的，然而从dom元素的冒泡事件，这本身就是不可避免地会触发，这是它的硬伤。

> 1）当然也想过用**e.stoppropagation()**阻止冒泡，然并卵~。
> 2）通过**拖拽距离**判断是否属于拖拽还是点击，然并卵~
> 3）通过**拖拽时间**判断是属于拖拽还是点击，好像是能解决绝大部分场景……

```jsx
class TestTab extends Component {
  constructor (props) {
    super(props)
    this.lx = 5
    this.rx = 60
    this.ty = 50
    this.state = {
      x: 10,
      y: 60,
    }
  }

  handleOnMouseDown (e) {
    //……
    const downTime = Date.now()
    this.moveTime = 0

    document.onmousemove = (ev) => {
      //……
      let cx = ev.clientX
      let cy = ev.clientY
      const dx = Math.max(0, this.maxX - this.rx)
      const dy = Math.max(0, this.maxY - this.ty)
      cx = Math.max(cx, this.lx)
      cy = Math.max(cy, this.ty)
      cx = Math.min(cx, dx)
      cy = Math.min(cy, dy)
      this.setState({
        x: cx,
        y: cy
      })
    }
    document.onmouseup = (ev) => {
      this.moveTime = Date.now() - downTime // 拖拽时长
      //……
    }
  }

  showTip (e) {
    // console.log(e.clientX, e.clientY)
    console.log("拖拽时长:", this.moveTime)
    if (this.moveTime && this.moveTime < 150) { // 小于150ms才认为是点击事件，否则认为是拖拽事件
      //……业务代码
    }
  }

  render () {
    const { x, y } = this.state
    const ww = window.innerWidth || window.clientWidth
    const wh = window.innerHeight || window.clientHeight
    this.maxX = ww
    this.maxY = wh

    return (
      <div className='parent' style={{ left: x, top: y }} onMouseDown={e => this.handleOnMouseDown(e)}>
        <div className='child' style={{ 'height': '200px', 'width': '300px', 'border': '1px solid red' }} onClick={e => this.showTip(e)}>
        </div>
      </div>
    )
  }
}
```
上述，通过拖拽时长小于150ms的认为是点击事件，超过这个时长的认为是拖拽事件，好像是能解决绝大部分场景，然并卵~~

最终，这都是旁门左道投机取巧而已~~并不完善，最终坑的还是用户体验

## 3、基于drag技术
用drag技术解决才是最终的王道，抛开浏览器的兼容性问题，目前绝大部分主浏览器都已经兼容H5的drag方法，在caniuse上查询对比mousedown和drag，后者还更优一些。

事件流程：
> 1）ondragstart：触发拖拽起始位置
> 2）ondrag：移动拖拽（可选，可略过这一步）
> 3）ondragend：定位拖拽目标位置，实现拖拽

```jsx
class TestTab extends Component {
  constructor (props) {
    super(props)
    this.state = {
      x: 0,
      y: 0
    }
  }

  _onDragStart (e) {
    // console.log('_onDrag:', e.clientX, e.clientY)
  }

  _onDrag (e) {
    // console.log('_onDrag:', e.clientX, e.clientY)
  }

  _onDragEnd (e) {
    // console.log('_onDragEnd:', e.clientX, e.clientY)
    this.setState({
      x: e.clientX,
      y: e.clientY
    })
  }

  showTip (e) {
    // console.log("onclick")
  }

  render () {
    const { x, y } = this.state
    return (
      <div className='parent' style={{ left: x, top: y }} draggable
        onDragStart={e => this._onDragStart(e)}
        onDrag={e => this._onDrag(e)}
        onDragEnd={e => this._onDragEnd(e)}>
        <div className='child' style={{ 'height': '200px', 'width': '300px', 'border': '1px solid red' }} onClick={e => this.showTip(e)}>
        </div>
      </div>
    )
  }
}
```
上述，即可实现最简单的拖拽div效果，并且拖拽父元素parent过程中绝对不会触发子元素child的click点击事件。完美解决基于mousedown技术实现拖拽留下的硬伤。

然而，这个时候又会出现新的问题

> 1）拖拽位置不准确，即从A位置拖拽到B位置，最终的位置不准确，误差拖拽时起始的鼠标坐标。假设A（2，2），起始鼠标拖拽位置（3，3），期望目标B（9，9），最终实际目标为（8，8），与期望目标位置不符。这是由于拖拽的起始鼠标位置引起的，拖拽的div本身尺寸越大，效果就越明显，最终通过计算可以解决该问题，如下

```jsx
class TestTab extends Component {
  constructor (props) {
    super(props)
    this.lx = 5
    this.rx = 60
    this.ty = 50
    this.state = {
      x: 10,
      y: 60
    }
  }

  _onDragStart (e) {
    // console.log('_onDragStart:', e.clientX, e.clientY)
    const { x, y } = this.state
    this.sx = e.clientX - x
    this.sy = e.clientY - y
  }

  _onDrag (e) {
    // console.log('_onDrag:', e.clientX, e.clientY)
  }

  _onDragEnd (e) {
    // console.log('_onDragEnd:', e.clientX, e.clientY)
    let cx = e.clientX
    let cy = e.clientY
    cx = cx - this.sx
    cy = cy - this.sy

    // 解决限制拖拽区域问题
    const dx = Math.max(0, this.maxX - this.rx)
    const dy = Math.max(0, this.maxY - this.ty)
    cx = Math.max(cx, this.lx)
    cy = Math.max(cy, this.ty)
    cx = Math.min(cx, dx)
    cy = Math.min(cy, dy)

    this.setState({
      x: cx,
      y: cy
    })
  }

  showTip (e) {
    // console.log("onclick")
  }

  render () {
    const { x, y } = this.state
    const ww = window.innerWidth || window.clientWidth
    const wh = window.innerHeight || window.clientHeight
    this.maxX = ww
    this.maxY = wh

    return (
      <div className='parent' style={{ left: x, top: y }} draggable
        onDragStart={e => this._onDragStart(e)}
        onDrag={e => this._onDrag(e)}
        onDragEnd={e => this._onDragEnd(e)}>
        <div className='child' style={{ 'height': '200px', 'width': '300px', 'border': '1px solid red' }} onClick={e => this.showTip(e)}>
        </div>
      </div>
    )
  }
}
```
上述，基本解决遇到几个问题

## 4、总结
1）基于mousedown技术开发拖拽div，存在实际业务的缺陷，虽然可以通过技巧解决，但并非是最好的解决方案
2）基于drag技术开发拖拽div，相对来说不失为一种更好的解决方案，并且代码更精简，简约而不简单

