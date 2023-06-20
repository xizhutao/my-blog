---
title: 【node 报错】Cannot read properties of undefined (reading ‘body‘)
description: 在写更新用户信息接口进行表单的信息进行验证时，报错：'Cannot read properties of undefined (reading 'body')'
headimg: /images/node.jpeg
date: 2022-04-16
tags:
  - Node
categories:
  - Node
---

{% asset_img body1.png %}

## 🏆 前言

在写更新用户信息接口进行表单的信息进行验证时，报错：
`Cannot read properties of undefined (reading 'body')`
字面意思是不能读取"body 属性"，查找后发现是导入定义的表单验证规则的第三方模块失败，终端打印结果为 undefined。
==解决并排错过程==：首先打印了导入的第三方验证规则模块，发现结果是 undefined，此时定位错误到：获取的第三方验证规则模块不是一个对象；

## 🥇 一、解决办法

错误原因：导入的第三方验证规则模块的 exports 对象上挂载了两个验证规则对象，导入第三方验证规则模块后，获取的 exports 对象中有两个验证规则，在使用其中的一个验证规则时，需要以下面的格式获取：

```javascript
//导入表单验证规则，此时获取的update_userinfo_schema为一个包含两个验证规则的对象；
const update_userinfo_schema = require('../schema/user')
//定义更新用户信息的路由，以update_userinfo_schema.UpdateUserinfoSchema方式获取到验证规则
router.post('/userinfo', expressJoi(update_userinfo_schema.UpdateUserinfoSchema), userinfohandler.UpdateUserInfo)
```

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
