---
title: React.memo 和 useMemo 的使用
description: 本文讲解如何使用 useMemo()、React.memo 减少不必要的reRender
headimg: /images/react.jpeg
categories:
  - React
date: 2021-03-15
tags:
  - React
sidebar: [toc] # 放置任何你想要显示的侧边栏部件
---

## 问题背景

大家在使用 React 框架进行开发时一定遇到过以下问题：

- 当函数式组件中的某一状态改变，整个组件刷新，重新渲染
- 在类组件中 setState() 时，整个组件也会重新渲染
  以上问题若不进行优化，导致的结果是：
- 随着代码的增加，每次的状态改变，页面进行一次 reRender ，这将产生很多不必要的 reRender 不仅浪费性能，而且会导致页面卡顿；

## useMemo 进行优化

以下面 App 组件进行分析

```javascript
import './App.css'
import ProfileTest from './components'
import {Profiler, useEffect, useMemo, useState, useRef} from 'react'
function App() {
  const [name, setName] = useState('')
  const [num, setNum] = useState(0)
  useEffect(() => {
    setTimeout(() => {
      console.log('111')
      setName('xxx')
    }, 2000)
  }, [])
  const memoVal = useMemo(() => {
    console.log('运行了useMemo num值为：', num)
    return num + 1
  }, [num])
  console.log('memoVal值为：', memoVal)
  console.log('父组件运行分割线----------------------------------------------------')
  const changeNum = () => {
    setNum(2)
  }
  return (
    <Profiler id="profile-test">
      <div className="App">
        {/* <ProfileTest /> */}
        <button style={{marginTop: 100}} onClick={changeNum}>
          改变num
        </button>
      </div>
    </Profiler>
  )
}
export default App
```

以上组件在首次渲染、以及 2 秒后的执行结构如下图所示：
{% asset_img memoopt1.png %}
很显然首次渲染执行了，useMemo，而 2 秒后有状态变化后没有执行 useMemo。
点击按钮改变 useMemo 的依赖项后可以发现，如下图所示 useMemo 又执行了。
{% asset_img memoopt2.png %}
因此在使用函数式组件时，可以使用 useMemo 减少不必要的 reRender 提高组件的性能；

## React.memo 进行优化

在以上组件的基础上，给 App 增加一个子组件，代码如下所示：

```javascript
import React from 'react'

export default function Children(props) {
  console.log('子组件运行了，接收的props是', props)
  console.log('子组件渲染分割线------------------------------------------')
  return <div>子组件</div>
}
```

首次 render 以及 2s 后组件的 reRender 控制台打印结果如下图所示：
{% asset_img memoopt3.png %}
由上图可以看出，reRender 时 Children 组件的 props 并未变化，因此，此次 Children 组件的 reRender 是不必要的，需要进行优化；

### props 的值是基本类型

如果 Children 的 props 是基本类型，则可以做一下优化：

```javascript
import React, {memo} from 'react'

function Children(props) {
  console.log('子组件运行了，接收的props是', props)
  console.log('子组件渲染分割线------------------------------------------')
  return <div>子组件</div>
}
export default memo(Children)
```

优化后控制台打印如下信息，一下信息可以看出 Children 组件没有进行 reRender
{% asset_img memoopt4.png %}

### props 的值是引用类型

若子组件的 props 是引用类型 ，则需要进行深度比较，此时 React.memo()要传入第二个参数进行深度比较，改变后 Children 组件的代码如下所示：

```javascript
import React, {memo} from 'react'

function Children(props) {
  console.log('子组件运行了，接收的props是', props)
  console.log('子组件渲染分割线------------------------------------------')
  return <div>子组件</div>
}
export default memo(Children, (preProps, nextProps) => {
  return JSON.stringify(preProps) === JSON.stringify(nextProps)
})
```

以上 memo 第二个参数 ，通过比较 preProps 和 nextProps 返回一个布尔值，使得 props 进行深度比较；
`注意：React.memo的第二个参数进行深度比较时有一定开销，其产生的开销存在大于子组件reRender的可能`

## 写在最后

useMemo() 和 React.memo() 都是进行组件性能优化的方式，其区别是

- useMemo 可以进行更加细粒度的优化(有依赖项)
- React.memo() 可以控制 props 的浅比较和深度比较
- React.memo 在没有第二个参数的时候相当于 class 中的 PureComponent，当增加了第二个参数的时候相当于生命周期中的 shouldComponentUpdate；
  > 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
