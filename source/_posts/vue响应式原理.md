---
title: 深入理解Vue响应式原理
description: Vue响应式原理是Vue最独特的特性之一，当数据模型进行修改时，视图就会进行更新，这使得状态管理简单直接，但是其底层的细节还是需要我们深入学习理解，这样遇到一些问题我们才能快速进行定位，并解决；下面我将带大家由浅入深，逐步理解Vue的响应式原理；
headimg: /images/vue.png
date: 2021-07-04
tags:
  - Vue
categories:
  - Vue
---

## 前言

Vue 响应式原理是 Vue 最独特的特性之一，当数据模型进行修改时，视图就会进行更新，这使得状态管理简单直接，但是其底层的细节还是需要我们深入学习理解，这样遇到一些问题我们才能快速进行定位，并解决；下面我将带大家由浅入深，逐步理解 Vue 的响应式原理；
{% asset_img vue.png %}

## 如何监测数据的变化

要想准确响应数据的变化，首先需要精确及时的监测数据的变化，Vue 检测数据的变化核心还是通过 Object.defineProperty();下面通过实例为大家详细阐述 Vue 检测数据变化的过程，代码示例如下：

```javascript
<script type="text/javascript" >
			let data = {
				name:'xizhutao',
				address:'安徽',
			}
			//创建一个监视的实例对象，用于监视data中属性的变化
			const obs = new Observer(data)
			console.log(obs)
			//准备一个vm实例对象
			let vm = {}
			vm._data = data = obs
			function Observer(obj){
				//汇总对象中所有的属性形成一个数组
				const keys = Object.keys(obj)
				//遍历
				keys.forEach((k)=>{
					Object.defineProperty(this,k,{
						get(){
							return obj[k]
						},
						set(val){
							console.log(`${k}被改了，我要去解析模板，生成虚拟DOM.....我要开始忙了`)
							obj[k] = val
						}
					})
				})
			}
		</script>
```

以上代码检测数据变化的思路如下：
一、封装一个 Observer 构造函数，接收一个 data 数据对象，通过 Object.keys()方法，将接收到的数据对象中的属性存放到一个数组中，然后调用 forEach()方法对数组进行遍历，用 Object.defineProperty()给 this 即 Observer 实例对象添加数组中的每一项属性；
二、通过 get、set 方法监测数据的操作，一旦 Observer 构造函数的实例对象上的属性被访问，get 函数就会被调用，返回 data 数据中的属性值，同样的 Observer 构造函数的实例对象上的属性被修改了，data 数据中的属性值也实现了修改；这就是 Vue 监测数据变化的原理；

## 注意事项

### 对象

Vue 不能监测对象的 Property 的添加和删除，在监测数据变化原理中讲到，在 Vue 初始化实例对象时，会将 data 对象中的属性存到数组中进行遍历，然后调用 Object.defineProperty()给属性添加 get ， set 方法监视 data 对象中属性的变化，因此如果我们在初始化实例对象后，给 Vue 实例添加属性，并不会给添加的属性添加对应的 get、set 方法，导致添加的属性并不是响应式的；如果你还不理解，请看以下示例代码：

```javascript
var vm = new Vue({
  data: {
    a: 11
  }
})
// vm.a 是响应式的
vm.b = 22
// vm.b 是非响应式的
```

以上代码中的 a 属性在初始化实例对象时就有，因此会生成相对应的 get、set 方法，然而 b 属性是在初始化 vm 实例后添加的属性，因此并没有生成相对于的 set 和 get 方法；导致属性 b 并不是响应式的；
针对以上问题我们可以使用 Vue.set(object, propertyName, value)或者 vm.$set ( target，propertyName/index，value)方法解决，但是此方法只能给嵌套的对象添加属性，不能给 vm 或 vm 的根数据对象添加响应式属性；
代码示例如下：

```javascript
//someObject为嵌套在data数据对象内的一个对象
Vue.set(vm.someObject, 'b', 22)
vm.$set(this.someObject, 'b', 22)
```

⚠ 注意：set 方法不能给 vm 或 vm 的根数据对象设置属性，只能给跟数据对象中嵌套的对象设置属性 ❗❗❗

### 数组

Vue 不能监测以下数组的变动：1、通过索引号修改数组的对应值，修改后的值不是响应式的；2.通过 length 修改数组长度时，数据不是响应式的；示例如下：

```javascript
var vm = new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
})
vm.items[1] = 'x' // 不是响应性的
vm.items.length = 2 // 不是响应性的
```

为了解决以上通过索引添加的数据不是响应式的，可以使用以下方法解决：
==1、Vue.set(vm.items, indexOfItem, newValue)==
==2、vm.$set(vm.items, indexOfItem, newValue)==
==3、vm.items.splice(indexOfItem, 1, newValue)==
为了解决通过 length 属性改变数组长度也是响应式的，可以使用以下方法：
==vm.items.splice(newLength)==
代码示例如下：

```javascript
var vm = new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
})
Vue.set(vm.items, 0, 'aa') // 响应性的
vm.$set(vm.items, 0, 'aa') // 响应性的
vm.items.splice(0, 1, 'aa') //响应性的
//通过length
vm.items.splice(2) //响应性的
```

更详细的信息请查看 Vue.js 官网：[深入理解响应式原理](https://cn.vuejs.org/v2/guide/reactivity.html)

## 写在最后

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
