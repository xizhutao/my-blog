---
title: React Fiber树的构建和替换过程
description: 此文讲解在底层 React Fiber 树的构建和替换过程；
headimg: /images/react.jpeg
date: 2021-04-25
tags:
  - fiber
categories:
  - React
---

## 前言

React Fiber 树的创建和替换过程运用了[双缓存](https://baike.baidu.com/item/%E5%8F%8C%E7%BC%93%E5%86%B2)技术，即先在内存中创建 fiber 树，待 fiber 树创建完成以后，直接将旧的 fiber 树替换成新的 fiber 树，这样做的好处是省去了直接在页面上渲染时的计算时间，避免计算量大导致的白屏、卡顿，现在你一定还不太理解，下面进行详细讲解！

## mount 过程

以一下 demo 为例进行讲解：

```javascript
function App() {
  const [num, add] = useState(0)
  return <p onClick={() => add(num + 1)}>{num}</p>
}

ReactDOM.render(<App />, document.getElementById('root'))
```

- 首次渲染时：
  执行 `ReactDOM.render(<App/>, document.getElementById('root'));` 创建一个应用的根节点 `fiberRootNode` 和 App 组件的根节点 rootFiber，`fiberRootNode`节点会通过 current 指针指向 rootFiber ；此时构建的 fiber 树叫 current fiber 树，没有子节点；
  {% asset_img fiber1.png %}

- render 阶段：
  此阶段会在内存中构建一颗 workInProgress fiber 树，构建时会与 current fiber 树进行 diff ,复用 current fiber 树的 fiber 节点，被复用的 fiber 节点通过 alternate 指针连接；此时的 fiber 树如下图所示：
  {% asset_img fiber2.png %}

- commit 阶段：
  由于内容在内存中已经构建完成，下一步就是将内存中的 fiber 树渲染成真实 DOM ，此后 React 底层将 fiberRootNode 的 current 指针指向了新构建的 fiber 树，此时 workInprogress fiber 树变成了 current fiber 树；
  此时的 fiber 树结构如下图：
  {% asset_img fiber3.png %}

## update 过程

此时点击，p 元素，此时状态改变，会触发更新，再次进入 render 阶段，在内存中创建一棵 workInProgress fiber 树，并且和 current fiber 树进行 diff 复用 current fiber 树节点，最后进入 commit 阶段 ，将构建的 fiber 树渲染到页面上，此后 fiberRootNode 的 current 指针指向 workInProgress fiber 树，更新后的 fiber 树如下所示：
{% asset_img fiber4.png %}
参考：[React 技术揭秘](https://react.iamkasong.com/process/doubleBuffer.html#%E4%BB%80%E4%B9%88%E6%98%AF-%E5%8F%8C%E7%BC%93%E5%AD%98)

## 写在最后

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
