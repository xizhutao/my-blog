---
title: 【node 报错】后端写的接口，postman测试，正常返回数据，前端发起ajax请求报错----Not Found 404
description: 在将后台管理系统的接口写完，并使用postman测试无问题后， 在前端发起ajax请求，结果报错，报错码为：Not Found 404
headimg: /images/node.jpeg
date: 2022-04-10
tags:
  - Node
categories:
  - Node
---

## 🥇 前言

在将后台管理系统的接口写完，并使用 postman 测试无问题后， 在前端发起 ajax 请求，结果报错，报错码为：
`Not Found 404`
![在这里插入图片描述](https://img-blog.csdnimg.cn/ff21869b30984adf8fa5e7ba25e7c791.png#pic_center)
{% asset_img notfound1.png %}

## 排错过程

首先返回为 404 ，首先想到的是路径不对，找不到，但是 postman 又测试没问题，说明 postman 里面的路径是对的，前端发起 ajax 请求的路径是错误，postman 请求的数据是手动输入，而前端发起 ajax 请求的数据是动态获取的，所以将错误定位到了，前端在请求数据拼接路径出错，即前端在获取提交的参数出错；最后排错又定位到如下的 id 参数获取失败，发现数据库中的 id 中的 i 为大写的 I，而在前端获取 Id 属性时，用的小写；
![在这里插入图片描述](https://img-blog.csdnimg.cn/1aee3e52cb6747098c94997664b8a1e9.png#pic_center)
{% asset_img notfound2.png %}
![在这里插入图片描述](https://img-blog.csdnimg.cn/ecca619b8cd9495190f37c32ea45750d.png#pic_center)
{% asset_img notfound3.png %}
![在这里插入图片描述](https://img-blog.csdnimg.cn/645a7ae5dacf4dc6a74df95362462d17.png#pic_center)
{% asset_img notfound4.png %}

最终统一 Id 后问题解决；

## 🥇 知识の总结

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
