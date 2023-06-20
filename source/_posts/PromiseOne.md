---
title: 手写 Promise (一)
description: 本篇文章将带领大家一步步封装自己的Promise；
headimg: /images/promise.jpeg
date: 2021-06-01
tags:
  - Promise
categories:
  - Promise
---

## 前言

众所周知由于多层回调函数相互嵌套，代码耦合性太强，牵一发而动全身，导致代码难以维护，可读性差，所以为了解决回调地狱问题，ES6 新增了 Promise 的概念。相信内置的 Promise 大家都会使用了，但是 Promise 实现的原理，大家一定还很陌生，本篇文章将带领大家一步步封装自己的 Promise；

{% asset_img promise1.png %}

## 手写 Promise 前必读

在手写 Promise 之前我们需要了解 Promise 的三个状态，Promise 是一个有状态的对象，可能处于如下 3 种状态之一：
 ==待定（pending）==
 ==兑现（fulfilled，有时候也称为“解决”，resolved）==
 ==拒绝（rejected）==
待定（pending）是 Promise 的最初始状态。在待定状态下，Promise 可以落定（settled）为代表成功的兑现（fulfilled）状态，或者代表失败的拒绝（rejected）状态。无论落定为哪种状态都是==不可逆==的。只要从待定转换为兑现或拒绝，Promise 的状态就不再改变。而且，也不能保证期约必然会脱离待定状态。因此，组织合理的代码无论 Promise 解决（resolve）还是拒绝（reject），甚至永远处于待定（pending）状态，都应该具有恰当的行为。重要的是，Promise 的状态是私有的，不能直接通过 JavaScript 检测到。这主要是为了避免根据读取到的期约状态，以同步方式处理 Promise 对象。另外，Promise 的状态也不能被外部 JavaScript 代码修改。这与不能读取该状态的原因是一样的：Promise 故意将异步行为封装起来，从而隔离外部的同步代码。
📣 注意：在 Promise 的状态发生改变后就会发生状态凝固，即状态不能再次改变；

## 🥇 实现 Promise 的基本功能

首先我们先用内置 Promises 创建一个实例对象，传入一个函数，内部打印 11，运行后发现控制台输出了 11，说明传入 Promise 的函数在 new 一个 Promise 实例时，会立即调用；
{% asset_img promise2.png %}
那么我们如何实现此功能呢？
实际上在创建 Promise 实例对象时传入的参数叫做执行器函数（executor），若想 new 一个 Promise 的同时调用函数，执行器的函数的调用必须写在 Promise 类的 constructor 函数内部，这样每次创建 Promise 实例时，都会执行 constructor 函数，即可调用 executor 函数；在调用执行器函数的同时我们需要传入 resolve、reject 函数用来处理 Promise 的状态，状态即为前面提到的三种状态；
代码示例如下所示：

```javascript
class Promise {
  constructor(executor) {
    //调用执行器函数
    executor(resolve, reject)
  }
}
```

我们将此 js 代码引入一个 html 页面覆盖内置的 Promise 测试此功能；如果能正常打印 11 即为成功；
{% asset_img promise3.png %}
实现以上功能以后我们接着定义构造器函数(executor)内部的两个处理 Promise 状态的函数 resolve 和 reject，在定义两个状态处理函数前我们要先初始化 Promise 的初始状态为 pending(待定),而且要先定义 resolve 和 reject 函数执行成功的结果和执行失败的原因；
代码示例如下：

```javascript
class Promise {
  constructor(executor) {
    this.state = 'pending' //Promise初始状态
    this.value = undefined //执行成功的结果
    this.reason = undefine //执行失败的结果
    //调用执行器函数
    executor(resolve, reject)
  }
}
```

在定义 Promise 的两个改变状态的函数前我们需要了解 Promise/A+规范的关于状态凝固的概念，即 resolve 和 reject 函数处理 Promise 状态是不可逆的，即执行完函数改变 Promise 状态后不能再次改变 Promise 的状态即状态凝固了；
基于规范的状态改变的不可逆性，我们需要在函数内部加上判断条件，即当 Promise 的状态为 pending 时才能进入代码块执行代码；代码示例如下：

```javascript
class Promise {
  constructor(executor) {
    this.state = 'pending' //Promise初始状态
    this.value = undefined //执行成功的结果
    this.reason = undefine //执行失败的结果

    // 定义resolve函数
    const resolve = (value) => {
      if (this.state === 'pending') {
        this.state = 'fulfiled'
        this.value = value
      }
    }
    //定义reject函数
    const reject = (reason) => {
      if (this.state === 'pending') {
        this.state = 'rejected'
        this.reason = reason
      }
      //调用执行器函数
      executor(resolve, reject)
    }
  }
}
```

实现以上功能后，我们需要对代码进行优化，即执行器函数调用时，可能发生一些未知的错误，为了避免程序因为抛出错误导致程序崩溃，我们将执行器函数调用放到 try-catch 语句中；
优化后代码如下：

```javascript
//捕获Promise状态发生错误的第三种方式
try {
  //调用执行器函数
  executor(resolve, reject)
} catch (err) {
  reject(err)
}
```

## 🥇 then()方法的实现

在 Promise 状态发生改变后，无论成功后者失败，都会触发 then 内部的回调函数；然后根据 Promise 改变后的状态，去判断是执行成功的回调函数还是失败的回调函数；代码示例如下：

```javascript
class Promise {
  constructor(executor) {
    this.state = 'pending' //Promise初始状态
    this.value = undefined //执行成功的结果
    this.reason = undefine //执行失败的结果

    // 定义resolve函数
    const resolve = (value) => {
      if (this.state === 'pending') {
        this.state = 'fulfiled'
        this.value = value
      }
    }
    //定义reject函数
    const reject = (reason) => {
      if (this.state === 'pending') {
        this.state = 'rejected'
        this.reason = reason
      }

      //调用执行器函数
      executor(resolve, reject)
    }
  }

  then(onResolved, onRejected) {
    //Promise状态为resolved时
    if (this.state === 'resolved') {
      onResolved(this.value)
    }

    //Promise状态为rejected时
    if (this.state === 'rejected') {
      onRejected(this.reason)
    }
  }
}
```

定义完 then()方法后进行测试，发现在执行 resolve 或者执行 reject 函数改变 Promise 状态时，如果函数的调用是异步的，就会导致在调用 then 方法时，异步的代码还未执行，Promise 状态还是 pending，就无法执行 then 方法内部的成功和失败的回调函数；
测试失败的 demo 如下：

```javascript
var myP = new Promise(function (resolve, reject) {
  console.log('执行')
  setTimeout(function () {
    reject(3)
  }, 1000)
})

myP.then(
  function (res) {
    console.log(res)
  },
  function (err) {
    console.log(err)
  }
)
```

因为以上 reject(3) ，在定时器里面是异步的，而 then()方法是同步的，所以控制台并未打印出 3；执行流程如下图所示；
{% asset_img promise4.png %}
解决此问题思路，在 then 方法内若判断 Promise 状态是 pending，说明改变 Promise 状态的函数是异步的，还未执行，此时我们直接将失败和成功后调用的回调函数存到一个数组中，当执行完异步队列中的改变 Promise 状态的函数时再进行调用；优化后的 then()

```javascript
then(onResolved, onRejected) {
    //Promise状态为resolved时
    if (this.state === 'resolved') {
      onResolved(this.value)
    }

    //Promise状态为rejected时
    if (this.state === 'rejected') {
      onRejected(this.reason)
    }

    // 如果状态为pending，将回调函数存如数组
    if (this.state === 'pending') {
      //将执行成功的回调函数存入数组
      this.resolvedCallback.push(() => {
        onResolved(this.value)
      })
    }
    //将执行失败的回调函数存入数组
    this.rejectedCallback.push(() => {
      onRejected(this.reason)
    })
  }
```

以上的 resolvedCallback、rejectedCallback 为定义的两个空数组；将回调函数存入数组后，在执行异步队列中的函数时，遍历数组，然后再执行数组中存的成功或失败的回调函数；
优化 resolve 和 reject 函数后的完整代码如下所示；

```javascript
class Promise {
  constructor(executor) {
    this.state = 'pending' //Promise初始状态
    this.value = undefined //执行成功的结果
    this.reason = undefine //执行失败的结果
    //定义数组用来存放回调函数
    this.resolvedCallback = []
    this.rejectedCallback = []
    // 定义resolve函数
    const resolve = (value) => {
      if (this.state === 'pending') {
        this.state = 'fulfiled'
        this.value = value
        //  改变完状态执行数组中的回调函数
        this.resolvedCallback.forEach((item) => item())
      }
    }
    //定义reject函数
    const reject = (reason) => {
      if (this.state === 'pending') {
        this.state = 'rejected'
        this.reason = reason
        // 改变完状态执行数组中的回调函数
        this.rejectedCallback.forEach((item) => item())
      }

      //调用执行器函数
      executor(resolve, reject)
    }
  }

  then(onResolved, onRejected) {
    //Promise状态为resolved时
    if (this.state === 'resolved') {
      onResolved(this.value)
    }

    //Promise状态为rejected时
    if (this.state === 'rejected') {
      onRejected(this.reason)
    }

    // 如果状态为pending，将回调函数存如数组
    if (this.state === 'pending') {
      //将执行成功的回调函数存入数组
      this.resolvedCallback.push(() => {
        onResolved(this.value)
      })
    }
    //将执行失败的回调函数存入数组
    this.rejectedCallback.push(() => {
      onRejected(this.reason)
    })
  }
}
```

此时就完成了 then()方法的包括支持异步的功能；本片文章就讲到这里，下篇文章将接着带领大家实现 then 方法的链式调用功能；

## 写在最后

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
