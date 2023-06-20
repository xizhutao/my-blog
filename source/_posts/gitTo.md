---
title: Git分布式版本控制系统(二)
description: 应很多粉丝的要求，本篇文章将带小伙伴们学习Git分支、Github相关的知识；
headimg: /images/git.jpeg
date: 2022-02-22
tags:
  - Git
categories:
  - Git
---

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞

**前言**

&nbsp;&nbsp;&nbsp;&nbsp;上一篇文章已经讲解了 Git 基本命令的使用，应很多粉丝的要求，本篇文章将带小伙伴们学习 Git 分支、Github 相关的知识，大家也可以在评论区说出自己对本文的意见，和小二一起交流学习，学习完本篇文章，相信小伙伴们将会有醍醐灌顶，入浴春风的感觉哦！！！
{% asset_img to1.webp %}

@[TOC](文章の目录)

## 🏆 学习の目标

1.  了解 Github
2.  会将本地仓库传到远程仓库
3.  会将远程仓库克隆到本地
4.  学会使用 Git 分支操作

## 一、Github

### 了解开源相关概念

#### 什么是开源和闭源

&nbsp;&nbsp;&nbsp;&nbsp;==开源==即开放源代码，代码是公开的，任何人都能够去查看、修改、使用的，相反的==闭源==，它的代码是封闭的，只有作者能看到闭源软件的代码，只有作者才能对源代码进行修改；

#### 什么是开源许可协议

&nbsp;&nbsp;&nbsp;&nbsp;开源并不意味着完全没有限制，为了限制使用者的使用范围和保护作者的权利，每个开源项目都应该遵守开源许可协仪（Open Source License ）。

#### 常见的 5 种开源许可协议

① BSD（Berkeley Software Distribution）
② Apache Licence 2.0
③ GPL（GNU General Public License）
⚫ 具有传染性的一种开源协议，不允许修改后和衍生的代码做为闭源的商业软件发布和销售
⚫ 使用 GPL 的最著名的软件项目是：Linux
④ LGPL（GNU Lesser General Public License）
⑤ MIT（Massachusetts Institute of Technology, MIT）
⚫ 是目前限制最少的协议，唯一的条件：在修改后的代码或者发行包中，必须包含原作者的许可信息
⚫ 使用 MIT 的软件项目有：jquery、Node.js

#### 为什么要拥抱开源

&nbsp;&nbsp;&nbsp;&nbsp;开源的核心思想是“我为人人，人人为我”，人们越来越喜欢开源大致是出于以下 3 个原因：
① 开源给使用者更多的控制权
② 开源让学习变得容易
③ 开源才有真正的安全
开源是软件开发领域的大趋势，拥抱开源就像站在了巨人的肩膀上，不用自己重复造轮子，让开发越来越容易。

#### 开源项目托管平台

&nbsp;&nbsp;&nbsp;&nbsp;专门用于免费存放开源项目源代码的网站，叫做开源项目托管平台。目前世界上比较出名的开源项目托管平台
主要有以下 3 个：
⚫ Github（全球最牛的开源项目托管平台，没有之一）
⚫ Gitlab（对代码私有性支持较好，因此企业用户较多）
⚫ Gitee（又叫做码云，是国产的开源项目托管平台。访问速度快、纯中文界面、使用友好）
注意：以上 3 个开源项目托管平台，只能托管以 Git 管理的项目源代码，因此，它们的名字都以 Git 开头。

#### 什么是 Github

&nbsp;&nbsp;&nbsp;&nbsp;Github 是全球最大的开源项目托管平台。因为只支持 Git 作为唯一的版本控制工具，故名 GitHub。 在 Github 中，你可以：
① 关注自己喜欢的开源项目，为其点赞打 call
② 为自己喜欢的开源项目做贡献（Pull Request） ③ 和开源项目的作者讨论 Bug 和提需求 （Issues） ④ 把喜欢的项目复制一份作为自己的项目进行修改（Fork） ⑤ 创建属于自己的开源项目
⑥ etc…
So，Github ≠ Git

### 注册账号

#### 注册激活 Github 账号流程

① 访问 Github 的官网首页 https://github.com/
② 点击“Sign up”按钮跳转到注册页面
③ 填写可用的用户名、邮箱、密码
④ 通过点击箭头的形式，将验证图片摆正
⑤ 点击“Create account”按钮注册新用户
⑥ 登录到第三步填写的邮箱中，点击激活链接，完成注册；
{% asset_img to2.png %}
{% asset_img to3.png %}
{% asset_img to4.png %}

### 远程仓库的使用

#### 新建空白远程仓库

{% asset_img to5.png %}
{% asset_img to6.png %}

#### 远程仓库的两种访问方式

&nbsp;&nbsp;&nbsp;&nbsp;Github 上的远程仓库，有两种访问方式，分别是 HTTPS 和 SSH。它们的区别是：
①HTTPS：零配置；但是每次访问仓库时，需要重复输入 Github 的账号和密码才能访问成功
②SSH：需要进行额外的配置；但是配置成功后，每次访问仓库时，不需重复输入 Github 的账号和密码注意：在实际开发中，推荐使用 SSH 的方式访问远程仓库。

#### 基于 HTTPS 将本地仓库上传到 Github

{% asset_img to7.png %}

#### SSH key

SSH key 的作用：实现本地仓库和 Github 之间免登录的加密数据传输。SSH key 的好处：免登录身份认证、数据加密传输。
SSH key 由两部分组成，分别是：
①id_rsa（私钥文件，存放于客户端的电脑中即可）
②id_rsa.pub（公钥文件，需要配置到 Github 中）

#### 生成 SSH key

① 打开 Git Bash
② 粘贴如下的命令，并将your_email@example.com 替换为注册 Github 账号时填写的邮箱：
⚫ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
③ 连续敲击 3 次回车，即可在 C:\Users\用户名文件夹\.ssh 目录中生成 id_rsa 和 id_rsa.pub 两个文件；

#### 配置 SSH key

① 使用记事本打开 id_rsa.pub 文件，复制里面的文本内容
② 在浏览器中登录 Github，点击头像-> Settings-> SSH and GPG Keys -> New SSH key ③ 将 id_rsa.pub 文件中的内容，粘贴到 Key 对应的文本框中
④ 在 Title 文本框中任意填写一个名称，来标识这个 Key 从何而来

#### 检测 Github 的 SSH key 是否配置成功

打开 Git Bash，输入如下的命令并回车执行：
![在这里插入图片描述](https://img-blog.csdnimg.cn/d388e160c89445fba15ac048256e7759.png)
{% asset_img to8.png %}
上述的命令执行成功后，可能会看到如下的提示消息：
![在这里插入图片描述](https://img-blog.csdnimg.cn/4c06a6b3a3254a9cbadec45c24a8e288.png)
{% asset_img to9.png %}
输入 yes 之后，如果能看到类似于下面的提示消息，证明 SSH key 已经配置成功了：
![在这里插入图片描述](https://img-blog.csdnimg.cn/44d6bda9c0134acda1b7670c989cd4c7.png)
{% asset_img to10.png %}

#### 基于 SSH 将本地仓库上传到 Github

![在这里插入图片描述](https://img-blog.csdnimg.cn/3fb664f39cf04308bb61b71f89679413.png)
{% asset_img to11.png %}

#### 将远程仓库克隆到本地

打开 Git Bash，输入如下的命令并回车执行：
![在这里插入图片描述](https://img-blog.csdnimg.cn/7f0f14b18ede4502ab805c6ce2fc6218.png)
{% asset_img to12.png %}

## 二、Git 分支

### Git 本地分支操作

#### 分支的概念

&nbsp;&nbsp;&nbsp;&nbsp;分支就是科幻电影里面的平行宇宙，当你正在电脑前努力学习 Git 的时候，另一个你正在另一个平行宇宙里努力学习 SVN。
&nbsp;&nbsp;&nbsp;&nbsp;如果两个平行宇宙互不干扰，那对现在的你也没啥影响。不过，在某个时间点，两个平行宇宙合并了，结果，你既学会了 Git 又学会了 SVN！
![在这里插入图片描述](https://img-blog.csdnimg.cn/80a5846ff5de4197a50d1d424bba724a.png)
{% asset_img to13.png %}

#### 分支在实际开发中的作用

&nbsp;&nbsp;&nbsp;&nbsp;进行多人协作开发的时候，为了防止互相干扰，提高协同开发的体验，建议每个开发者都基于分支进行项目功能的开发，例如：
![在这里插入图片描述](https://img-blog.csdnimg.cn/a93cca4790b04c3aa26ba305bbec38bb.png)
{% asset_img to14.png %}

#### master 主分支

&nbsp;&nbsp;&nbsp;&nbsp;在初始化本地 Git 仓库的时候，Git 默认已经帮我们创建了一个名字叫做 master 的分支。通常我们把这个 master 分支叫做主分支。
![在这里插入图片描述](https://img-blog.csdnimg.cn/d164fcb49da74d3e901c4c6dbfa9debf.png)
{% asset_img to15.png %}
📣 注意：在实际工作中，master 主分支的作用是：==用来保存和记录整个项目已完成的功能代码==。
&nbsp;&nbsp;&nbsp;&nbsp;因此，不允许程序员直接在 master 分支上修改代码，因为这样做的风险太高，容易导致整个项目崩溃。❗❗❗

#### 功能分支

&nbsp;&nbsp;&nbsp;&nbsp;于程序员不能直接在 master 分支上进行功能的开发，所以就有了==功能分支==的概念。
&nbsp;&nbsp;&nbsp;&nbsp;==功能分支==指的是==专门用来开发新功能的分支==，它是临时从 master 主分支上分叉出来的，当新功能开发且测试完毕后，最终需要合并到 master 主分支上，如图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/e6f4c1d1fa294b2daf5083cf8caea765.png)
{% asset_img to16.png %}

#### 查看分支列表

![在这里插入图片描述](https://img-blog.csdnimg.cn/b2df95b6c7f64be59d39deb24fceacc5.png)
{% asset_img to17.png %}
运行的结果如下所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/92d62e24d9734c48944f5f4401600ce6.png)
{% asset_img to18.png %}
📣 注意：分支名字前面的\*号表示当前所处的分支。❗❗❗

#### 创建分支

&nbsp;&nbsp;&nbsp;&nbsp;使用如下的命令，可以基于当前分支，创建一个新的分支，此时，新分支中的代码和当前分支完全一样：
![在这里插入图片描述](https://img-blog.csdnimg.cn/b27891070f944531a8e51bf0c8e60287.png)
{% asset_img to19.png %}
图示如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/526910880c774bd5a03244bc3d743471.png)
{% asset_img to20.png %}

#### 切换分支

使用如下的命令，可以切换到指定的分支上进行开发：
![在这里插入图片描述](https://img-blog.csdnimg.cn/60fea47d94bd49df9255e33839d3b633.png)
{% asset_img to21.png %}
图示如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/aa09bb19128d4d69beb660230f29eda2.png)
{% asset_img to22.png %}

#### 分支的快速创建和切换

使用如下的命令，可以创建指定名称的新分支，并立即切换到新分支上：
![在这里插入图片描述](https://img-blog.csdnimg.cn/453ce0e3da4548dd83f68a57bd878040.png)
{% asset_img to23.png %}
图示如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/c52d590e54ad43ccb82f397fcb36a389.png)
{% asset_img to24.png %}
📣 注意：
"git checkout -b 分支名称" 是下面两条命令的简写形式：
①git branch 分支名称
②git checkout 分支名称 ❗❗❗

#### 合并分支

&nbsp;&nbsp;&nbsp;&nbsp;功能分支的代码开发测试完毕之后，可以使用如下的命令，将完成后的代码合并到 master 主分支上：
![在这里插入图片描述](https://img-blog.csdnimg.cn/8546a5c1d6304935bbc003b6b7130ed0.png)
{% asset_img to25.png %}
![在这里插入图片描述](https://img-blog.csdnimg.cn/cd9182a1084743e6aca6a029c12cc209.png)
{% asset_img to26.png %}
📣 合并分支时的注意点：
假设要把 C 分支的代码合并到 A 分支，则必须==先切换到 A 分支上==，再运行==git merge ==命令，来合并 C 分支 ❗❗❗

#### 删除分支

&nbsp;&nbsp;&nbsp;&nbsp;当把功能分支的代码合并到 master 主分支上以后，就可以使用如下的命令，删除对应的功能分支：
![在这里插入图片描述](https://img-blog.csdnimg.cn/cfb483361c524ecc94f27ec634629e4f.png)
{% asset_img to27.png %}
图示如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2932181c39da4075b314122004eb4a5a.png)
{% asset_img to28.png %}

#### 遇到冲突时的分支合并

&nbsp;&nbsp;&nbsp;&nbsp;如果在两个不同的分支中，对同一个文件进行了不同的修改，Git 就没法干净的合并它们。此时，我们需要打开这些包含冲突的文件然后==手动解决冲突==。
![在这里插入图片描述](https://img-blog.csdnimg.cn/955751fc8f6a4d2baaa4a530988e2b0a.png)

### Git 远程分支操作

#### 将本地分支推送到远程仓库

如果是第一次将本地分支推送到远程仓库，需要运行如下的命令：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20db9abbafcf420fa9d5de70b49b4b95.png)
{% asset_img to29.png %}
📣 注意：第一次推送分支需要带-u 参数，此后可以直接使用 git push 推送代码到远程分支。❗❗❗

#### 查看远程仓库中所有的分支列表

通过如下的命令，可以查看远程仓库中，所有的分支列表的信息：
![在这里插入图片描述](https://img-blog.csdnimg.cn/7fee94661fdb49c89d86dcdbae44d37e.png)
{% asset_img to30.png %}

#### 跟踪分支

跟踪分支指的是：从远程仓库中，把远程分支下载到本地仓库中。需要运行的命令如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/cfc337af43f1478faa21103e5979854e.png)
{% asset_img to31.png %}

#### 拉取远程分支的最新的代码

可以使用如下的命令，把远程分支最新的代码下载到本地对应的分支中：

![在这里插入图片描述](https://img-blog.csdnimg.cn/f27a90332acc4ebea8d82f9bbe4991d4.png)
{% asset_img to32.png %}

#### 删除远程分支

可以使用如下的命令，删除远程仓库中指定的分支：
![在这里插入图片描述](https://img-blog.csdnimg.cn/b0b02ff6106146e08b6ee5777589f74e.png)
{% asset_img to33.png %}

## 知识の总结

&nbsp;&nbsp;&nbsp;&nbsp;看完以上内容小伙伴们一定觉得内容多记不住吧，没关系哦，小二已经为您总结了以后项目常用的命令，大家一定要牢记哦！！！
![在这里插入图片描述](https://img-blog.csdnimg.cn/591c2d5860e44d528990de0ba0d569ef.png#pic_center)
{% asset_img to34.png %}

## 🥇 推荐の内容

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞

本期为小伙伴们推荐上一期的 Git 基本命令的使用哦，配合本篇文章大家一定会对 Git 有更深刻的理解哦！！下一期我将为大家带来==Git 冲突的解决方法以及案例==，大家敬请期待哟！！!
[Git 分布式版本控制系统一](https://blog.csdn.net/qq_44588612/article/details/124477260?spm=1001.2014.3001.5501)
