---
title: watch 和 computed 的区别
description: watch 和 computed 是vue实例对象中的两个重要属性，watch是监视属性，用来监视vue实例对象上属性和方法的变化，computed被称为计算属性，可以将data对象中的属性进行计算得到新的属性；由于watch属性监视data对象中的属性变化时，也能拿到data对象中的属性进行计算；因此产生疑问既然两者都能实现同一功能，两者有什么区别呢？下面将以此疑问为突破点结合实例详述两者的区别；
headimg: /images/vue.png
date: 2021-08-16
tags:
  - Vue
categories:
  - Vue
---

## 前言

watch 和 computed 是 vue 实例对象中的两个重要属性，watch 是监视属性，用来监视 vue 实例对象上属性和方法的变化，computed 被称为计算属性，可以将 data 对象中的属性进行计算得到新的属性；由于 watch 属性监视 data 对象中的属性变化时，也能拿到 data 对象中的属性进行计算；因此产生疑问既然两者都能实现同一功能，两者有什么区别呢？下面将以此疑问为突破点结合实例详述两者的区别；

## watch 实现案例

```javascript
  <!-- 准备好一个容器-->
    <div id="root">
      姓：<input type="text" v-model="firstName" /> <br /><br />
      名：<input type="text" v-model="lastName" /> <br /><br />
      全名：<span>{{fullName}}</span> <br /><br />
    </div>
  </body>
  <script type="text/javascript">
    Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。
    const vm = new Vue({
      el: '#root',
      data: {
        firstName: '张',
        lastName: '三',
        fullName: '张-三',
      },
      watch: {
        firstName(val) {
          setTimeout(() => {
            this.fullName = val + '-' + this.lastName
          }, 1000)
        },
        lastName(val) {
          setTimeout(() => {
            this.fullName = this.firstName + '-' + val
          }, 1000)
        },
      },
    })
  </script>
```

以上为 watch 通过监视 data 中的 firstName 和 lastName 的变化改变 fullName

## computed 实现案例

```javascript
<body>
    <!-- 准备好一个容器-->
    <div id="root">
      姓：<input type="text" v-model="firstName" /> <br /><br />
      名：<input type="text" v-model="lastName" /> <br /><br />
      全名：<span>{{fullName}}</span> <br /><br />
    </div>
  </body>

  <script type="text/javascript">
    Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

    const vm = new Vue({
      el: '#root',
      data: {
        firstName: '张',
        lastName: '三',
      },
      computed: {
        fullName() {
          return this.firstName + '-' + this.lastName
        },
      },
    })
```

以上为通过 computed 计算属性实现 fullName 和 firstName、lastName 产生联系；

## 对比

通过对比可以看出，watch 和 computed 都能实现相同的功能，但是首先第一眼看去，computed 计算属性实现的案例明显代码更少更精简；但是 watch 能将 fullName 的变化改成异步；因此对 watch 和 computed 属性做出如下总结：
1、computed 能完成的功能 watch 也能完成
2、watch 能完成的功能 computed 不一定能完成，比如 watch 可以进行异步操作；
📣 注意： 1.所被 Vue 管理的函数，最好写成普通函数，这样 this 的指向才是 vm 或 组件实例对象。 2.所有不被 Vue 所管理的函数（定时器的回调函数、ajax 的回调函数等、Promise 的回调函数），最好写成箭头函数，这样 this 的指向才是 vm 或 组件实例对象。

## 写在最后

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
