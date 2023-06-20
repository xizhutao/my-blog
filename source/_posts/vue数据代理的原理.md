---
title: Vue数据代理的原理
description: Vue的数据代理的原理是十分重要的，它的学习对我们了解Vue的底层工作原理以及学习Vue具有举足轻重的作用，掌握了数据代理的原理，我们便可以由浅入深，再去学习Vue的响应式原理；因此本篇文章我将带领大家详细的了解Vue实现数据代理的核心----Object.defineProperty();
headimg: /images/vue.png
date: 2021-08-04
tags:
  - Vue
categories:
  - Vue
---

## 前言

Vue 的数据代理的原理是十分重要的，它的学习对我们了解 Vue 的底层工作原理以及学习 Vue 具有举足轻重的作用，掌握了数据代理的原理，我们便可以由浅入深，再去学习 Vue 的响应式原理；因此本篇文章我将带领大家详细的了解 Vue 实现数据代理的核心----Object.defineProperty();
{% asset_img vueproxy1.jpeg %}

## 何为数据代理

首先在讲解 Vue 数据代理的原理之前，我们应该知道什么是数据代理；==数据代理的定义是==：一个对象操作(读写)另一个对象中的属性和方法。定义比较抽象我们可能难以理解，下面我将结合实例向大家讲述什么是数据代理，实例代码如下：

```javascript
//  数据代理：通过一个对象代理对另一个对象中属性的操作（读/写）
let obj = {x: 100}
let obj2 = {y: 200}
Object.defineProperty(obj2, 'x', {
  get() {
    return obj.x
  },
  set(value) {
    obj.x = value
  }
})
```

从以上代码上可以看出实现数据代理的核心是使用 Object.defineProperty()方法，当访问 obj2 上的 x 属性时，就会调用 get 方法读取 obj 的属性，当我们修改了 obj2 上 x 属性时，就会调用 set 方法，并且传入修改的值 value 值，将 obj 的 x 属性改为 value；以上我们相当与通过 obj2 对象操作了 obj 对象中的 x 属性，这样便定义中所说的一个对象代理了另一个对象中的属性的操作；

## Vue 中的数据代理

详细大家通过上一节的学习，已经了解了什么是数据代理，下面我将结合下面实例带大家来看看 Vue 中的数据代理；代码示例如下：

```javascript
 <body>
    <!--
				1.Vue中的数据代理：
							通过vm对象来代理data对象中属性的操作（读/写）
				2.Vue中数据代理的好处：
							更加方便的操作data中的数据
				3.基本原理：
							通过Object.defineProperty()把data对象中所有属性添加到vm上。
							为每一个添加到vm上的属性，都指定一个getter/setter。
							在getter/setter内部去操作（读/写）data中对应的属性。
		 -->
    <!-- 准备好一个容器-->
    <div id="root">
      <h2>学校名称：{{name}}</h2>
      <h2>学校地址：{{address}}</h2>
    </div>
  </body>

  <script type="text/javascript">
    Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。
    let data = {
      name: 'xizhutao',
      address: '中国',
    }
    const vm = new Vue({
      el: '#root',
      data,
    })
  </script>
```

从数据代理的定义上说是一个对象代理了另一个对象的读写操作，因此我们首先要找到 Vue 中的两个对象，其次是哪个对象代理了哪个对象的操作；如下图所示我们在 new Vue()时产生了一个 vm 实例对象，传入的参数内部又有一个 data 对象；
{% asset_img vueproxy2.png %}
那么以上两个对象是谁代理谁呢，下面通过控制面板演示是 vm 实例对象代理了 data 对象；首先在控制面板执行以下代码修改 vm 实例对象上的 name 和 address 属性；
{% asset_img vueproxy3.png %}
执行完以上代码后我们打开 vue 的调试面板，发现 data 上定义的属性确实变成了我们在 vm 实例上修改的值；即我们通过修改 vm 实例对象上的属性，实现了修改 data 对象的属性，即实现了数据代理，这就是 Vue 中的数据代理；
{% asset_img vueproxy4.png %}

## 写在最后

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
