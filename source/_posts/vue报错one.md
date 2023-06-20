---
title: 【Vue报错】Object.prototype method ‘hasOwnProperty‘ from target object no-prototype-builtins
description: no-prototype-builtins规则限制了我们在目标对象上通过原型链使用Object上的方法；下面我将为大家提供解决办法；
headimg: /images/vue.png
date: 2021-10-14
tags:
  - Vue
categories:
  - Vue
---

## 前言

在判断一个对象中是否有一个属性时，我调用了 Object 对象上的 hasOwnProperty()接口时，控制台报错报错信息如下：
`Do not  access Object.prototype method 'hasOwnProperty' from target object no-prototype-builtins`,把此错误信息翻译过来是：目标对象没有 Object 原型对象上的方法 hasOwnProperty 的使用权，规则是 no-prototype-builtins；意思就是 no-prototype-builtins 规则限制了我们在目标对象上通过原型链使用 Object 上的方法；下面我将为大家提供解决办法；

## 方法一

```javascript
let events = {'some-index': false}
let key = 'some-index'
if (Object.prototype.hasOwnProperty.call(events, key)) {
  // This would compile without any issue !
  console.log('The object has the property')
}
```

此方法很显然绝对不会出现问题，直接使用 Object.prototype.hasOwnProperty.call(events, key)调用接口，然后用 call 方法改变 this 指向，将 this 指向目标对象，传入要判断的参数 key 就将问题解决；

## 方法二

```javascript
let events = {'some-index': false}
let key = 'some-index'
if (!!Object.getOwnPropertyDescriptor(events, key)) {
  // This would compile without any issue !
  console.log('The object has the property')
}
```

此方法是直接调用了 getOwnPropertyDescriptor(events, key)这个方法也能解决问题；

## 方法三

最后一种是最暴力的方法，直接将 ESlint 的配置项 lintOnSave 改为 false 重新启动以下 VSCode 也能解决问题

```javascript
const {defineConfig} = require('@vue/cli-service')
module.exports = defineConfig({
  transpileDependencies: true,
  lintOnSave: false
})
```

## 写在最后

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
