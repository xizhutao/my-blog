---
title: dataPick 组件日期格式在不同浏览器的兼容性
description: 此篇文章为作者在使用antd-mobile组件库的DatePicker组件时，遇到的日期格式在不同浏览器的兼容问题；
headimg: /images/antd.jpeg
categories:
  - AntDesign
date: 2021-05-25
tags:
  - antDesign
sidebar: [toc] # 放置任何你想要显示的侧边栏部件
---

## 前言

此篇文章为作者在使用 antd-mobile 组件库的 DatePicker 组件时，遇到的问题，希望对大家有所帮助，问题如下图所示；
{% asset_img dataPick.png %}

## 问题描述

如上图所示，在使用组件库的 DatePicker 组件，年份未显示出来，而月份和日是能够正常显示的，起初认为是因为年份太多，导致渲染不出来(年份到 2099)，后来发现`是因为使用new Date()获取日期实例时，必须写成new Date(2099/12/31)这种格式，才能显示出来，如果使用new Date(2099-12-31)格式，则年份显示不出来，这便是问题的直接原因；`
代码如下：

```javascript
//起初：const now = new Date('2099-12-31')
const now = new Date('2099/12/31') //解决
```

## 写在最后

本次问题的本质原因是兼容性问题，即 ios 只支持 new Date(xx/xx/xx)这种格式创建日期实例，而不支持 new Date(xx-xx-xx)这种格式创建日期实例，最终导致了日期组件的年份渲染不完全；

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
