---
title: React-redux-dom 刷新页面数据丢失问题
description: 对于React-redux-dom的使用，大家一定是赞赏有加，它的集中式状态管理，使得共享的数据形成一个状态树，任何一个组件需要数据时都可以随时从状态树上访问数据，但是react-redux-dom存在的一个特点是当我们刷新页面重新渲染界面时，reducer返回的初始化状态会将现有的状态树覆盖，导致数据丢失的问题；下面我将结合实例为大家提供解决这个问题的思路；
headimg: /images/redux.jpeg
date: 2021-03-14
tags:
  - Redux
categories:
  - Redux
---

## 前言

对于 React-redux-dom 的使用，大家一定是赞赏有加，它的集中式状态管理，使得共享的数据形成一个状态树，任何一个组件需要数据时都可以随时从状态树上访问数据，但是 react-redux-dom 存在的一个特点是当我们刷新页面重新渲染界面时，reducer 返回的初始化状态会将现有的状态树覆盖，导致数据丢失的问题；下面我将结合实例为大家提供解决这个问题的思路；

## 数据丢失问题导致的 401 错误

**控制报错**
{% asset_img reduxdata1.gif %}
redux 开发者工具中显示一刷新数据就丢失，如下图所示
{% asset_img reduxdata2.gif %}

## 问题分析及解决

上述刷新后 redux 开发者工具中显示数据丢失，显然是我在前言中所描述的，当刷新页面时，reducer 会初始化状态，导致数据丢失，若想解决此问题我们显然需要和本地存储一起做个持久化，即 redux 管理的数据存储到 locationStorage 中，当刷新页面时，reducer 初始化的数据先从本地存储中找，若找到了就返回，到 redux 中，找不到再返回空字符串或者对象；代码示例如下：

```javascript
import {getToken} from '@/utils/token'
/**
 * 处理用户登录和退出的action
 * @param {string} preState
 * @param {object} action
 * @returns
 */
const initState = getToken() || ''
const userlogin = (preState = initState, action) => {
  switch (action.type) {
    case 'login/setToken':
      return action.payload
    case 'user/logout':
      return ''
    default:
      return preState
  }
}
export default userlogin
```

以上的 getToken || "" ,就是先从本地存储中拿初始化数据(getToken 是封装好的从本地存储中拿数据的函数)，若没有 getToken()，直接给初始化的状态一个空字符串就会导致每次刷新，redux 中的数据丢失；
对 redux 不是很熟的同学可以先到 redux 官方文档学习 redux，其链接如下：
[redux 中文文档](https://www.redux.org.cn/)

## 写在最后

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
