---
title: 深入理解Vuex的五大模块
description: vuex是用来集中管理组件依赖的数据，很好的解决了组件之间通信麻烦不易维护的缺点；下面我将结合Vue.js官网带领大家深入学习Vuex的五大模块；
headimg: /images/vue.png
date: 2021-11-16
tags:
  - Vuex
categories:
  - Vuex
---

## 前言

相信大家在学习组件通信时，多个组件之间进行通信，会存在父传子，子传父，兄弟传兄弟等等情况的发生，多个组件互相通信容易造成逻辑混乱，数据不好管理，复用性差的情况，而 Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化；总结一句话就是：Vuex 是用来集中管理组件依赖的数据，很好的解决了组件之间通信麻烦不易维护的缺点；下面我将结合 Vue.js 官网带领大家深入学习 Vuex 的五大模块；
{% asset_img vuex1.png %}

## state 模块

如上原理图所示，其中的 state 模块是用来定义和存放数据的，如果使用次数不多，可以使用$store.state.数据名去读取 state 中存放的数据；其中的 store 是 Vuex 模块的管理者，无论调用 dispatch 还是 commit 都需要 store。多次使用建议用 mapState 引入，然后在 computed 中用...展开使用，mapState 本身是一个函数，调用传递一个数组，要用哪像直接将名字写到数组里就行，他的返回值是一个对象，对象里存的就是想要用到的数据的计算属性使用：直接写 this.数据名字就行；

## actions 模块

actions 模块用于响应组件中的动作一般代码的逻辑写在 actions 中，比如我需要点击一个按钮 500 毫秒以后提交给下一个模块操作，此时可以给 commit 操作写在一个定时器里面，500 毫秒以后再调用 commit 方法；

## mutations 模块

此模块用于修改 state 模块中的数据，actions 模块调用 commit 方法就能直接将数据传到 mutations 模块中，mutations 模块可以根据收到的数据，直接修改 state 模块中储存的数据；

## getters 模块

此模块用于对 state 的中存储的数据进行加工处理，有点类似于 computed 计算属性；

## store 模块

store 如上原理图所示，是用于管理其它模块的，在调用 dispatch 和 commit 方法时，都需要使用$store;
**dispatch**
{% asset_img vue2.png %}
**commit**
{% asset_img vuex3.png %}

## 写在最后

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
