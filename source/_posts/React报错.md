---
title: 【React 报错】Failed to parse source map
description: 在react中的组件中要引入antd，当引入完运行后报错报错信息为：`Failed to parse source map`,那么如何解决的这个问题呢，下面我为大家提供解决办法；
headimg: /images/react.jpeg
date: 2021-04-28
tags:
  - React
categories:
  - React
---

## 前言

在 react 中的组件中要引入 antd，当引入完运行后报错报错信息为：`Failed to parse source map`,那么如何解决的这个问题呢，下面我为大家提供解决办法；
{% asset_img reacterror1.png %}
以上图片中的路径在 node_modules 中查找显然是可以查找到的，说明引入的路径没有问题，antd 也确认下载，大部分小伙伴一定和我一样纠结很久，实际的解决过程下面讲解

## 解决报错过程

首先我认为是 webpack 的配置没有配置好，我直接运行了==npm run eject== 调出了 webpack 的配置文件，在里面直接搜索 source map 配置项，并未发现什么问题，纠结许久，我直接将引入的 antd.css 换成 antd.min.css,问题解决；
{% asset_img reacterror2.png %}
对于为什么是必须引入压缩过的.min.css 文件，我很是纠结，欢迎大家评论区留言交流

## 写在最后

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
