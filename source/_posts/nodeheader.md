---
title: 【node 报错】Cannot set headers after they are sent to the client（消息头被发送到客户端后不能设置消息头）
description: 在写后端接口，进行用户注册验证时，控制台报错。报错信息：Cannot set headers after they are sent to the client
headimg: /images/node.jpeg
date: 2022-04-20
tags:
  - Node
categories:
  - Node
---

## 🥇 在写后端接口，进行用户注册验证时，控制台报错。报错信息：

`Cannot set headers after they are sent to the client`
{% asset_img hea1.png %}
用户注册验证代码如下：
{% asset_img hea2.png %}

## 🥇 原因分析及解决办法

{% asset_img hea3.png %}
最终将 32 行注释即解决问题，问题本质原因是 send()冲突，导致返回客户端两个信息

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
