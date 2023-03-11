---
title: antd Table 表格滚动高度适配
description: 在开发一个后台管理系统，某一页面用到了table表格，产品的需求是：table表格无论展示多少条数据，表头和分页信息要展示出来,此时我们一定首先想到了table表格的 scroll 属性，然后问题是 ：scrollY一定是动态的才能适配不同的设备高度；
headimg: /images/ava.jpeg
categories:
  - React
date: 2020-03-21
tags:
  - antDesign
sidebar: [toc] # 放置任何你想要显示的侧边栏部件
---

## 问题产生背景

在开发一个后台管理系统，某一页面用到了 table 表格，产品的需求是：

- table 表格无论展示多少条数据，表头和分页信息要展示出来
  此时我们一定首先想到了 table 表格的 scroll 属性，然后问题是 ：
- scrollY 一定是动态的才能适配不同的设备高度
  {% asset_img scroll1.png %}

## 如何解决

在解决这个问题之前我们脑海里必须先有一个动态获取表格竖向滚动高度的公式：

- scrollY = 窗口高度 — 表头底部到窗口顶的高度 — 分页高度
  如下图所示：
  {% asset_img scroll2.png %}
  此后问题又来了：
- 如何获取上图中的表头底到窗口定的距离呢？
  此时推荐一个 api`getBoundingClientRect()`, 此 api 可以动态获取元素相对视口的位置，详细用法见下 MDN 链接：
  [getBoundingClientRect](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getBoundingClientRect)
  有了这个 api 问题便迎刃而解，代码如下所示：

```javascript
/**
 * 获取第一个表格的可视化高度
 * @param {*} extraHeight 额外的高度(表格底部的内容高度 Number类型,默认为74)
 * @param {*} id 当前页面中有多个table时需要制定table的id
 */
export function getTableScroll({extraHeight, id}) {
  if (typeof extraHeight == 'undefined') {
    //  默认底部分页64 + 边距10
    extraHeight = 74
  }
  let tHeader = null
  if (id) {
    tHeader = document.getElementById(id)
      ? document.getElementById(id).getElementsByClassName('ant-table-thead')[0]
      : null
  } else {
    tHeader = document.getElementsByClassName('ant-table-thead')[0]
  }
  //表格内容距离顶部的距离
  let tHeaderBottom = 0
  if (tHeader) {
    tHeaderBottom = tHeader.getBoundingClientRect().bottom
  }
  //窗体高度-表格内容顶部的高度-表格内容底部的高度
  // let height = document.body.clientHeight - tHeaderBottom - extraHeight
  let height = `calc(100vh - ${tHeaderBottom + extraHeight}px)`
  return height
}
```

具体项目中可以将以上函数封装成一个工具函数，在页面初始化时调动，即可调用函数动态获取 scrollY，代码如下所示：

```javascript
const [scrollY, setScrollY] = useState("")
 //页面加载完成后才能获取到对应的元素及其位置
 useEffect(() => {
    setScrollY(getTableScroll())
  }, [])


<Table
            className="components-table-demo-nested"
            columns={columns}
            expandable={{ expandedRowRender }}
            dataSource={data}
            scroll={{y:scrollY}}
/>
```

## 写在最后

以上便是解决 antd table 表格滚动区域高度适配的方法，如果大家有更好的方法欢迎留言交流~~；

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
