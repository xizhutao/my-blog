---
title: Promise 如何控制并发
description: 某一个项目页面十分复杂，进入页面初始化时，产生了很多的http请求，造成了并发请求数量过多，可能会造成调用栈的内存溢出，导致页面崩溃，因此需要对请求数量做并发限制；
headimg: /images/promise.jpeg
date: 2021-05-29
tags:
  - Promise
categories:
  - Promise
---

## 问题产生背景

某一个项目页面十分复杂，进入页面初始化时，产生了很多的 http 请求，造成了并发请求数量过多，可能会造成调用栈的内存溢出，导致页面崩溃，因此需要对请求数量做并发限制；

## 实现方式

### 方式一

```javascript
//自定义请求函数
const request = (url) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(`请求${url}完成`)
    }, 1000)
  }).then((res) => {
    console.log('外部逻辑', res)
  })
}

//添加请求
const addRequest = (url) => {
  let requestTask = request(url)
  container.push(requestTask)
  requestTask.then((res) => {
    //请求结束后将该 Promise 任务从并发池中移除
    pool.splice(container.indexOf(requestTask), 1)
    console.log(`${url} 结束，当前并发数：${container.length}`)
    url = urls.shift()
    //每当并发池完成一个任务，同时添加一个任务
    if (url !== undefined) {
      addRequest(url)
    }
  })
}

let requestUrls = ['xxx1.com', 'xxx2.com', 'xxx3.com', 'xxx4.com', 'xxx5.com', 'xxx6.com', 'xxx7.com'] // 请求地址
let container = [] //并发请求容器
let max = 3 //最大并发请求数量
//先循环把并发池塞满
while (container.length < max) {
  let requestUrl = requestUrls.shift()
  addTask(requestUrl)
}
```

### 方式二

```javascript
//自定义请求函数
const request = (url) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(`请求${url}完成`)
    }, 1000)
  }).then((res) => {
    console.log('外部逻辑', res)
  })
}

//添加请求任务
const addTask = (url) => {
  let task = request(url)
  pool.push(task)
  task.then((res) => {
    //请求结束后将该 Promise 任务从并发池中移除
    pool.splice(pool.indexOf(task), 1)
    console.log(`${url} 结束，当前并发数：${pool.length}`)
  })
}
////每当并发池完成一个任务，同时添加一个任务
const run = (hasFinishTask) => {
  hasFinishTask.then((res) => {
    let url = requestUrls.shift()
    if (url !== undefined) {
      addTask(url)
      run(Promise.race(container))
    }
  })
}

let requestUrls = ['xxx1.com', 'xxx2.com', 'xxx3.com', 'xxx4.com', 'xxx5.com', 'xxx6.com', 'xxx7.com'] // 请求地址
let container = [] //并发请求容器
let max = 3 //最大并发请求数量
//先循环把并发池塞满
while (container.length < max) {
  let requestUrl = requestUrls.shift()
  addTask(requestUrl)
}
//利用 Promise.race 方法来获得并发池中某任务完成的信号
let hasFinishTask = Promise.race(container)
run(hasFinishTask)
```

## 写在最后

以上提供的两种方式，区别在于方式二使用了 Promise.race() 方法获取并发池中完成任务的信号，并删除任务，重新添加任务；两种方式的统一思路为：
• 初始化一个数组存放并发请求
• 初始化一个最大并发数变量
• 循环将并发请求的数组塞满
• 在每发起一次请求后删除本次请求并添加新的请求到并发请求的数组中
以上就是实 Promise 并发请求的思路，大家有其他实现方法欢迎留言交流

```

```
