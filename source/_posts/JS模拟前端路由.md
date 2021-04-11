---
title: JS模拟前端路由
data: 2021-04-11 14:02:49
toc: true
tags: 前端
---
## 路由是什么?

* 从用途上来说, 路由就是通过切换不同的URL向用户展示不同的界面内容.
* 从原理上来说, 路由是URL对函数或者资源的映射.
<!-- more -->

## 前端路由和后端路由

* 在后端路由中,每次切换URL都向服务器发送请求,服务器响应请求返回页面,这个URL可能对应一次资源读取或者是数据库操作,总之路由内容实际上是由后端决定的,当项目比较大时,后端开发的业务量会比较大,由于每次URL变化都要发送网络请求,页面加载耗时长,后端路由的用户体验也不好,并且也无法实现前后端分离开发.

* 前端路由即由前端决定路由的分发,这些路由通常对应着进行一些DOM操作,这样访问不同的路由,就可以显示不同的DOM结构.前端路由无需刷新页面,用户体验比较好,并且路由的路径和后台没有直接关系,由前端决定访问的内容或者对应的操作,可以实现前后端的分离开发.

## JS模拟前端路由
> 前端路由主要有两种方式, hash 和 history API.
* **hash 通过切换 "#" 后的内容实现路由,本文通过 hashchange 监听 hash 的改变,通过 location.hash 改变hash**
* **history API 的url更美观,无需 "#", 而是通过 history.pushState 和 history.replaceState实现URL的变化, 以pushState 为例, 执行函数后会向浏览器历史添加一个 state ,当浏览器触发动作时(前进后退),触发 window.popstate 事件, 可以看出, pushState 改变URL时并不会主动触发popstate事件, 所以无法实现主动的路由,解决办法是 包装 pushState 事件,使其被触发时主动分发一个事件,然后再监听事件做对应操作**


### CODE ⇩⇩⇩
#### index.html
``` html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>hash router</title>
  <script src="history.js" defer></script>
  <!-- <script src="hash.js" defer></script> -->
</head>

<body>
  <div class="container">
    <button @click="router1"
            id="btn1">page 1</button>
    <button @click="router2"
            id="btn2">page 2</button>
    <button @click="router3"
            id="btn3">page 3</button>
    <div id="content"></div>
  </div>
  <style>
    .container {
      margin: 200px auto;
      text-align: center;
    }
  </style>
</body>

</html>
```
#### hash.js

``` javascript
/**
 * create by coody
 */

const btn1 = document.getElementById('btn1')
const btn2 = document.getElementById('btn2')
const btn3 = document.getElementById('btn3')
const contentContainer = document.getElementById('content')

const content1 = document.createElement('h3')
content1.innerText = 'This is content1'
const content2 = document.createElement('h3')
content2.innerText = 'This is content2'
const content3 = document.createElement('h3')
content3.innerText = 'This is content3'

// 点击按钮, 向 location 添加对应 hash
btn1.addEventListener('click', () => {
  window.location.hash = 'content1'
})
btn2.addEventListener('click', () => {
  window.location.hash = 'content2'
})
btn3.addEventListener('click', () => {
  window.location.hash = 'content3'
})
// 清除 contentContainer 的子节点
function clearChild() {
  let cList = contentContainer.children
  if (cList !== null) {
    for (let i = 0; i < cList.length; i++) {
      contentContainer.removeChild(cList[i])
    }
  }
}
// 监听 hash 变化, 触发相应 DOM 变动
window.onhashchange = () => {
  switch (location.hash) {
    case '#content1':
      clearChild()
      contentContainer.appendChild(content1)
      break
    case '#content2':
      clearChild()
      contentContainer.appendChild(content2)
      break
    case '#content3':
      clearChild()
      contentContainer.appendChild(content3)
      break
    default:
      break
  }
}
```
#### history.js
``` javascript
/**
 * creat by coody
 */

const btn1 = document.getElementById('btn1')
const btn2 = document.getElementById('btn2')
const btn3 = document.getElementById('btn3')
const contentContainer = document.getElementById('content')

const content1 = document.createElement('h3')
content1.innerText = 'This is content1'
const content2 = document.createElement('h3')
content2.innerText = 'This is content2'
const content3 = document.createElement('h3')
content3.innerText = 'This is content3'

// 包装原来的 replaceState ,使其执行后自动分发同名事件
const historyWrap = function (eventName) {
  const origEvent = history[eventName]
  const e = new Event(eventName)
  return function () {
    const origRs = origEvent.call(this, ...arguments)
    e.arguments = arguments
    window.dispatchEvent(e)
    return origRs
  }
}

// 包装 replaceState
history.replaceState = historyWrap('replaceState')

btn1.addEventListener('click', () => {
  history.replaceState({ page: 1 }, '', '?content=1')
})
btn2.addEventListener('click', () => {
  history.replaceState({ page: 2 }, '', '?content=2')
})
btn3.addEventListener('click', () => {
  history.replaceState({ page: 3 }, '', '?content=3')
})

function clearChild() {
  let cList = contentContainer.children
  if (cList !== null) {
    for (let i = 0; i < cList.length; i++) {
      contentContainer.removeChild(cList[i])
    }
  }
}

// 监听分发的事件, 根据 state 判断对应页面内容
window.addEventListener('replaceState', () => {
  switch (history.state.page) {
    case 1:
      clearChild()
      contentContainer.appendChild(content1)
      break
    case 2:
      clearChild()
      contentContainer.appendChild(content2)
      break
    case 3:
      clearChild()
      contentContainer.appendChild(content3)
      break
    default:
      break
  }
})
```