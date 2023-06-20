---
title: 【node 报错】 res.cc is not a function
description: 在使用postman测试获取用户信息的接口时，登录的token字段错误，错误中间件捕捉不到错误信息，不能返回一个身份认证失败的对象，而是报错：`res.cc is not a function`
headimg: /images/node.jpeg
date: 2022-03-22
tags:
  - Node
categories:
  - Node
---

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞

## 🥇 终端报错信息如下图所示

{% asset_img cc1.png %}

## 前言

在使用 postman 测试获取用户信息的接口时，登录的 token 字段错误，错误中间件捕捉不到错误信息，不能返回一个身份认证失败的对象，而是报错：`res.cc is not a function`

## 🥇 排错过程

cc 函数是挂载在一个全局中间件上的一个处理错误信息的函数，找不到这个函数，首先想到的是这个函数定义错误，但是别的接口，可以利用 res.cc()正常返回错误信息，说明函数本身定义的没有问题；然后想到了 cc 函数挂载的全局中间件可能是位置错误，导致 cc 函数还未挂载到 res 上，已经发生了错误，当错误中间件捕捉到错误后，在 res 上访问不到 res.cc 函数,经调试解决;

**错误原因如下图所示，2 中间件应该放到 1 的前面：**
{% asset_img cc2.png %}

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
