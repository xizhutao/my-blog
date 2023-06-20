---
title: profile 性能优化工具
description: 项目开发时，难免会遇到一些组件卡顿的情况，那么如何能快速定位到问题组件、了解组件性能，并做出相应调整呢，本篇文章将以一个小 demo 为例，讲述 React 官方推荐的工具 Profiler;
headimg: /images/react.jpeg
categories:
  - React
date: 2021-02-21
tags:
  - Profile
sidebar: [toc] # 放置任何你想要显示的侧边栏部件
---

## 前言

项目开发时，难免会遇到一些组件卡顿的情况，那么如何能快速定位到问题组件、了解组件性能，并做出相应调整呢，本篇文章将以一个小 demo 为例，讲述 React 官方推荐的工具 Profiler;
demo 示例如下代码所示：

- 父组件

```javascript
import {useState} from 'react'
import './App.css'
import Display from './component/Display'
function App() {
  const [count, setCount] = useState(0)
  const [text, setText] = useState(0)
  const hangeChange = (e) => {
    setText(e.target.value)
  }
  console.log('父组件App渲染了--------------------------------')
  return (
    <div className="App">
      <div style={{marginTop: 50}}>
        <input value={text} onChange={hangeChange} />
        <br />
      </div>
      <Display text={text} />
      <div style={{marginTop: 50}}>
        <div style={{marginTop: 10}}> {count} </div>
        <button
          onClick={() => {
            setCount(count + 1)
          }}>
          +1
        </button>
      </div>
    </div>
  )
}

export default App
```

- 子组件

```javascript
import React, {memo} from 'react'
const Display = (props) => {
  const {text} = props
  console.log('子组件Display渲染了--------------------------------')

  return <div>{text}</div>
}
export default memo(Display)
```

## 组件渲染问题

打开应用后，看到如下界面：
{% asset_img profile1.png %}
进行如下操作：

- 在文本框的 0 后输入 123
- 点击三次+1 按钮

操作完成后，查看控制台输出：
{% asset_img profile2.png %}
由上图可以看出，在 input 框中输入 123 时，App 组件内部的状态改变，所以子组件 Display 的 porp 改变，因此父组件子组件依次渲染，然而当点击按钮+1 时，子组件 Display 的状态、prop 并未改变却进行了渲染；说明此组件的问题是进行了不必要的 re-render

## Profiler 查看 reRender 原因

如果你安装了 React-dev-tools 在 chrome 浏览器控制台可以打开 profiler 工具栏，如下图所示：
{% asset_img profile3.png %}

- 点击 reload 按钮
  {% asset_img profile4.png %}
  按以下步骤操作：
- 在文本框的 0 后输入 123
- 点击三次+1 按钮
- 点击 stop profiling 按钮
  {% asset_img profile5.png %}

此时我们可以看到工具记录的信息如下图所示：
{% asset_img profile6.png %}

- A 区记录了提交的次数
- B 区记录了应用内组件的状态
- C 区记录了每次提交的信息

我们点击按钮时，提交信息在最后三次，我们点击 A 区的最后一次提交，在 C 取查看提交信息：
{% asset_img profile7.png %}
从 B 区(光标放到组件上)、C 区的信息能看出 Display 组件的 re-render 是由于父组件的更新，此时我们可以考虑用 React.memo 包裹子 Display 组件进行优化，代码如下所示：

```javascript
import React, {memo} from 'react'
const Display = (props) => {
  const {text} = props
  console.log('子组件Display渲染了--------------------------------')
  return <div>{text}</div>
}
export default memo(Display)
```

代码优化后使用工具 reload，可以看到在最后一次提交时，尽管 App 组件更新，但是 Display 组件 Did not render，此时便达到了优化组件渲染的目的；
{% asset_img profile8.png %}
**参考：**
[Profiler 的使用](https://juejin.cn/post/7008337341634854942#heading-10)
[React Profiler 介绍](https://zh-hans.reactjs.org/blog/2018/09/10/introducing-the-react-profiler.html)

## 写在最后

在实际的项目中，如果遇到问题组件，我们应该学会使用 Profiler 组件定位问题组件，查看组件 re-render 原因有的放矢，这样才能提高解决问题的效率；
当然 React 官方也提供了 Profiler API，具体使用方法见以下链接：
[Profiler API](https://zh-hans.reactjs.org/docs/profiler.html#gatsby-focus-wrapper)

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
