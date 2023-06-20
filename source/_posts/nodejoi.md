---
title: 【node.js】报错Cannot mix different versions of joi schemas解决方法
description: 使用第三方包@hapi/joi 定义表单验证规则，然后利用postman检测到返回错误为：Cannot mix different versions of joi schemas
headimg: /images/node.jpeg
date: 2022-05-16
tags:
  - Node
categories:
  - Node
---

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞

## 🏆 前言

使用第三方包@hapi/joi 定义表单验证规则，然后利用 postman 检测到返回错误为：
`Cannot mix different versions of joi schemas`

{% asset_img joi1.png %}

## 🥇 报错原因：

@hapi/joi 第三方包不可用，需要下载其它版本；

## 🥇 解决办法：

运行如下命令重新安装第三方包

```javascript
npm i joi
```

将将导入的@hapi/joi 更改为 joi

```javascript
将
const joi = require("@hapi/joi")
改为：
const joi = require("joi")

```

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
