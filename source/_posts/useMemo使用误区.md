---
title: useMeno使用误区
date: 2023-03-09 19:48:39
tags:
---

## 问题产生背景

在开发一个后台管理系统，某一页面用到了 table 表格，产品的需求是：

- table 表格无论展示多少条数据，表头和分页信息要展示出来
  此时我们一定首先想到了 table 表格的 scroll 属性，然后问题是 ：
- scrollY 一定是动态的才能适配不同的设备高度
  {% asset_img scroll1.png %}
