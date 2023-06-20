---
title: 手写 Promise (二)
description: 本篇文章将继续带领大家一步步封装自己的Promise；
headimg: /images/promise.jpeg
date: 2021-06-04
tags:
  - Promise
categories:
  - Promise
---

## 前言

上一篇文章，我们已经实现 Promise 的基本功能和 then 方法的封装，解决了 then 方法中存在的异步问题；本篇文章我将带领大家实现 then 方法的链式调用，若想实现链式调用，首先我们要想到的是，then 方法必须要返回一个新的 Promise，这样 then 就能不断的调用，其次前一个 then 返回的值能够传给后一个 then；很显然上一篇文章封装的 then 方法实现不了, 这两个功能；这篇文章我将带领大家一步步完善 then 方法，实现 then 方法的链式调用；
{% asset_img promiseto1.png %}

## then 的链式调用

前言中已经描述了，要想实现链式调用分为两步， 首先 then 方法内部要返回一个新的 Promise，其次后一个 then 能接收前一个 then 的返回值；

**实现 then 方法内部返回一个 Promise**
在调用 then 方法时，立即 new 一个新的 Promise 实例，然后将实例返回出去，即直接在 then 方法内部 new 一个 Promise 对象，将上一篇文章封装的 then 方法剪切到新的 Promise 的处理器函数内部；
代码示例如下：

```javascript
then(onResolved, onRejected) {
    let promise2 = new Promise((resolve, reject) => {
      // 如果状态为成功， 执行onResolved函数
      if (this.state === 'fulfiled') {
        onResolved(this.value)
      }
      //如果状态为失败，执行onRejected函数
      if (this.state === 'rejected') {
        onRejected(this.reason)
      }

      //如果状态为pending，将回调函数存如数组
      if (this.state === 'pending') {
        //将执行成功的回调函数存入数组
        this.resolvedCallback.push(() => {
          onResolved(this.value)
        })
        //将执行失败的回调函数存入数组
        this.rejectedCallback.push(() => {
          onRejected(this.reason)
        })
      }
    })
    return promise2
  }
}
```

此时已经实现了每次调用 then 方法都能返回一个 Promise 实例，但是上一个 then 返回的结果和下一个 then 并没有产生联系，下面带大家继续完善；

**then 方法内部返回值传给下一个 then**
实现思路很简单就是将上一个 then 返回的值作为一个参数传入 resolve 或 reject 函数中并调用一下，这样下一个 then 就能接收上一个 then 返回的值；代码实例如下：

```javascript

  then(onResolved, onRejected) {
    let promise2 = new Promise((resolve, reject) => {
      // 如果状态为成功， 执行onResolved函数
      if (this.state === 'fulfiled') {
        const x = onResolved(this.value)
        resolve(x)
      }
      //如果状态为失败，执行onRejected函数
      if (this.state === 'rejected') {
        const x = onRejected(this.reason)
        reject(x)
      }

      //如果状态为pending，将回调函数存如数组
      if (this.state === 'pending') {
        //将执行成功的回调函数存入数组
        this.resolvedCallback.push(() => {
          const x = onResolved(this.value)
          resolve(x)
        })
        //将执行失败的回调函数存入数组
        this.rejectedCallback.push(() => {
          const x = onRejected(this.reason)
          reject(x)
        })
      }
    })
    return promise2
  }
```

以上便实现了 then 链式 调用的基本功能；

## x 的类型判断、循环引用的处理

以上已经实现了 then 链式的基本功能，但是仅支持返回数值的情况，在实际项目中往往前一个 then 返回的是一个 Promise 实例，此时下一个 then 接收的是一个 Promise 实例，demo 如下：

```javascript
var p = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(4)
  }, 0)
})
p.then((res) => {
  return new Promise(() => {
    resolve('ok')
  })
}).then((data) => {
  console.log(data)
})
```

以上的 demo 中第一个 then()中 resolve 在 Promise 实例的执行器函数中，在第二个 then 的函数中接收的是 Promise 实例对象；因此我们需要对上一个 then 返回的值的类型进行判断，如果返回的是 Promise 对象再调用一下 then 方法，如果返回的是普通值，直接 resolve 出去就行了，因为对第一个 then 返回的值的类型需要判断很多次，因此我们在外部封装一个函数，需要的时候调用就行了；代码示例如下所示：

```javascript
const resolvePromise = (x, resolve, reject) => {
  //x是Promise实例对象
  if (x instanceof Promise) {
    x.then(resolve, reject)
  } else {
    //x是普通值
    resolve(x)
  }
}
```

封装好函数后，我们将 then 方法内部的内部优化一下：

```javascript
then(onResolved, onRejected) {
    let promise2 = new Promise((resolve, reject) => {
      // 如果状态为成功， 执行onResolved函数
      if (this.state === 'fulfiled') {
        const x = onResolved(this.value)
        resolvePromise(x, resolve, reject)
      }
      //如果状态为失败，执行onRejected函数
      if (this.state === 'rejected') {
        const x = onRejected(this.reason)
        resolvePromise(x, resolve, reject)
      }

      //如果状态为pending，将回调函数存如数组
      if (this.state === 'pending') {
        //将执行成功的回调函数存入数组
        this.resolvedCallback.push(() => {
          const x = onResolved(this.value)
          resolvePromise(x, resolve, reject)
        })
        //将执行失败的回调函数存入数组
        this.rejectedCallback.push(() => {
          const x = onRejected(this.reason)
          resolvePromise(x, resolve, reject)
        })
      }
    })
    return promise2
  }
```

此时你一定以为 then 的链式调用已经完成，实际上还存在一个很棘手的问题，即==循环引用==，循环引用的 demo 如下图所示
{% asset_img promiseto2.png %}
如上图的 then 方法返回的值是它本身，这样会导致 p2 一值等待 then 方法的返回，造成了==循环引用==，下面我们将对循环引用进行处理；

**循环引用的处理**
此时需要 我们在外部封装的函数中对上一个 then 方法返回的值 x 进行判断，如果返回的值 x 全等于新 new 的 promise2，就直接抛出一个循环引用的错误 reject 出去，代码示例如下：

```javascript
const resolvePromise = (x, resolve, reject, promise2) => {
  // 处理循环调用问题
  if (x === promise2) {
    console.log(reject)
    return reject(reject(new TypeError('chaining cycle')))
  }
  //x是Promise
  if (x instanceof Promise) {
    x.then(resolve, reject)
  } else {
    //x是普通值
    resolve(x)
  }
}
```

此时运行后仍然会报错,很显然是因为调用上面的函数是同步的，在调用上面的函数，传入参数时，还拿不到 promise2，此时我们需要将调用 resolvePromise 函数处理成异步的，就能传入 promise2 这个参数；
代码示例如下：

```javascript
then(onResolved, onRejected) {
    let promise2 = new Promise((resolve, reject) => {
      // 如果状态为成功， 执行onResolved函数
      if (this.state === 'fulfiled') {
        //将内部代码处理异步，不然拿不到promise2
        setTimeout(() => {
          try {
            let x = onResolved(this.value)
            resolvePromise(x, resolve, reject, promise2)
          } catch (err) {
            reject(err)
          }
        }, 0)
      }
      //如果状态为失败，执行onRejected函数
      if (this.state === 'rejected') {
        setTimeout(() => {
          try {
            const x = onRejected(this.reason)
            resolvePromise(x, resolve, reject, promise2) //????????
          } catch (err) {
            reject(err)
          }
        }, 0)
      }

      //如果状态为pending，将回调函数存如数组
      if (this.state === 'pending') {
        //将执行成功的回调函数存入数组
        this.resolvedCallback.push(() => {
          setTimeout(() => {
            try {
              const x = onResolved(this.value)
              resolvePromise(x, resolve, reject, promise2)
            } catch (err) {
              reject(err)
            }
          })
        })
        //将执行失败的回调函数存入数组
        this.rejectedCallback.push(() => {
          setTimeout(() => {
            try {
              const x = onRejected(this.reason)
              resolvePromise(x, resolve, reject, promise2)
            } catch (err) {
              reject(err)
            }
          })
        })
      }
    })
    return promise2
  }
```

以上代码就解决了循环引用问题，下面我们来测试一下测试 demo 如下：

```javascript
const p = new Promise((resolve, reject) => {
  resolve(22)
})
const p2 = p.then((res) => {
  console.log(res)
  return p2
})
```

运行代码后控制台打印 22 ， 并抛出循环引用的错误即成功
{% asset_img promiseto3.png %}
本篇文章只讲到这里，下一篇文章将带领大家对代码进行优化，以及封装 Promise 的静态方法；

# 写在最后

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
