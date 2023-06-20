---
title: prop的单向数据流
description: 在Vue的子组件中去修改父组件通过prop传过来的对象时，控制台报错，报错信息如下：`error Unexpected mutation of “xxxx“ prop`，这背后的原因是什么呢，下面我将结合实际开发的报错信息和Vue.js官方文档带大家深入理解prop的单向数据流；
headimg: /images/vue.png
date: 2021-09-04
tags:
  - Vue
categories:
  - Vue
---

## 前言

在 Vue 的子组件中去修改父组件通过 prop 传过来的对象时，控制台报错，报错信息如下：`error Unexpected mutation of “xxxx“ prop`，这背后的原因是什么呢，下面我将结合实际开发的报错信息和 Vue.js 官方文档带大家深入理解 prop 的单向数据流；
{% asset_img data1.png %}

## 报错原因分析

代码如下所示：

```javascript
<script>
export default {
  data() {
    return {
      flag: '',
    }
  },
  //props传入的todo使用要加上this
  props: ['todo'],
  methods: {
    changeDone() {
      this.todo.done = this.flag
    },
  },
}
</script>
```

以上代码中可以看出此组件用 props 接收了一个 todo 对象，而我在 methods 对象中定义了一个函数，直接访问 todo 对象并且，修改了其中的 done 属性，导致了控制台报错；首先我们查阅官方文档看看，文档对 prop 的说明，文档中清楚的说明了 prop 的单向数据流，强调了父子组件之间形成了一个单向下行绑定；如果在子组件内部更改 prop，会导致浏览器报错；
{% asset_img data2.png %}

## 解决办法

对于以上错误，我们已经明确了原因，那么我们如果一定要修改父组件传入的数据，而且不影响父组件呢？下面我们来看看 Vue 官方文档提供的方法：
{% asset_img data3.png %}
以上图片看出 Vue 官方文档明确了两种变更 prop 的情形，第一种是在本地的 data 对象中添加属性，并且将 父组件传入的 prop 作为初始值；第二种是定义一个计算属性，用传入的 prop 属性进行计算；
**特别强调**：==注意在 JavaScript 中对象和数组是通过引用传入的，所以对于一个数组或对象类型的 prop 来说，在子组件中改变变更这个对象或数组本身将会影响到父组件的状态==。
更详细的信息请查看：[Vue.js 官方文章](https://cn.vuejs.org/v2/guide/components-props.html)

## 写在最后

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
