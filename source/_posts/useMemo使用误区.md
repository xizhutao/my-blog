---
pin: true
title: useMeno使用误区
description: 此文以实际开发遇到的问题为背景讲述 useMemo() 的使用误区；
headimg: /images/react.jpeg
date: 2021-04-21
tags:
  - hooks
categories:
  - React
---

## 问题背景

在某一个 h5 项目中，使用了 useMemo 对项目中的组件进行优化，减少组件不必要的 re-render, 优化后的结果：

- 在组件的 props 和状态未改变时，组件不再进行 re-render
  表面上看，这样优化没毛病，提高了组件性能，然后仔细阅读官方文档、以及网上一些大牛的文章，你会发现，你错了！！！
  实际上我们优化时忽略了一个问题：

- useMemo hook 本身的开销！
  下面将以使用此 hook 和未使用此 hook 的组件性能对比数据来说明，是否应该：
  `remove most of useMemo`

## useMemo 使用前后组件性能对比

**组件的复杂度为 1 时：**
{% asset_img memo1.png %}

- 使用 useMemo 时首次渲染和 re-render 时间都更长

**组件的复杂度为 100 时：**
{% asset_img memo2.png %}

- 首次渲染时，使用 useMemo 更耗时
- re-render 时 useMemo 稍快

**组件复杂度为 1000 时：**
{% asset_img memo3.png %}

- useMemo 在首次渲染时更慢
- re-render 时快了一点

**组件复杂度为 5000 时：**
{% asset_img memo4.png %}

- useMemo 首次渲染时更慢
- re-render 更快

## 结论

useMemo 本身的开销会随着组件的复杂度逐渐变大，导致首次渲染更慢，在实际项目中，我们应该`remove most of useMemo` 。

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
