---
title: React 组件的状态下移和内容提升
description: 本篇文章讲解两个简单实用的优化组件渲染性能的方法：状态下移、内容提升
headimg: /images/react.jpeg
date: 2021-04-28
tags:
  - React
categories:
  - React
---

## 前言

本专栏的另一篇文章，讲到了 useMemo 有一定的开销，不能滥用，本篇文章讲解两个简单实用的优化组件渲染性能的方法：

- 状态下移
- 内容提升

在讲解这两种方法之前，我们需要先手动创建一个有严重渲染性能的组件，如下所示：

```javascript
mport { useState } from 'react';

export default function App() {
  let [color, setColor] = useState('red');
  return (
    <div>
      <input value={color} onChange={(e) => setColor(e.target.value)} />
      <p style={{ color }}>Hello, world!</p>
      <ExpensiveTree />
    </div>
  );
}

function ExpensiveTree() {
  let now = performance.now();
  while (performance.now() - now < 100) {
    // Artificial delay -- do nothing for 100ms
  }
  return <p>I am a very slow component tree.</p>;
}
```

很显然，当 App 组件内的状态发生了改变，ExpensiveTree 组件会 re-render， 事实上 ExpensiveTree 组件的 props、state 并未发生改变，这并不是我们期望的结果，下面将提供两种简单的方法，提升组件渲染的性能；

## 状态下移

```javascript
export default function App() {
  let [color, setColor] = useState('red')
  return (
    <div>
      <input value={color} onChange={(e) => setColor(e.target.value)} />
      <p style={{color}}>Hello, world!</p>
      <ExpensiveTree />
    </div>
  )
}
```

我们可以看到以上 ExpensiveTree 组件并不依赖 App 组件内部的状态，因此我们是否可以考虑，将依赖 color 的元素抽离到一个依赖 color 的组件中呢？
下面是实践后的代码：

```javascript
export default function App() {
  return (
    <>
      <Form />
      <ExpensiveTree />
    </>
  )
}

function Form() {
  let [color, setColor] = useState('red')
  return (
    <>
      <input value={color} onChange={(e) => setColor(e.target.value)} />
      <p style={{color}}>Hello, world!</p>
    </>
  )
}
```

此时，将依赖 color 的元素提取到了 Form 组件中，Form 组件内部的状态不再影响 ExpensiveTree 组件的渲染，问题便得到了解决

## 内容提升

```javascript
export default function App() {
  let [color, setColor] = useState('red')
  return (
    <div style={{color}}>
      <input value={color} onChange={(e) => setColor(e.target.value)} />
      <p>Hello, world!</p>
      <ExpensiveTree />
    </div>
  )
}
```

以上情况是高开销组件 ExpensiveTree 的父节点依赖 color，此时显然状态下移是行不通的，但是还有另外一种办法：

```javascript
export default function App() {
  return (
    <ColorPicker>
      <p>Hello, world!</p>
      <ExpensiveTree />
    </ColorPicker>
  )
}

function ColorPicker({children}) {
  let [color, setColor] = useState('red')
  return (
    <div style={{color}}>
      <input value={color} onChange={(e) => setColor(e.target.value)} />
      {children}
    </div>
  )
}
```

此时和状态提升是完全相反的，将依赖 color 的元素提升到了 ColorPicker 组件中，而不依赖 color 的元素以 children prop 的形式传入了 ColorPicker ，当组件内部的状态改变时：

- ColorPicker 会重新渲染但是它仍然保存着
- ColorPicker 的 children 属性未变化 React 并不会访问那棵子树。 因此，ExpensiveTree 不会重新渲染。

## 写在最后

本文提供的方法并不是新的创意，大家一定都有所实践，但是我想表达的是，相比于滥用 useMemo，我们需要首先考虑使用这两种提高组件渲染性能的方法；

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
