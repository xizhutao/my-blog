---
title: Git分布式版本控制系统(一)
description: 分布式版本控制系统git是工作以后进行项目管理必不可少的工具，我将繁杂的命令进行了归类整理和总结，供大家参考学习；
headimg: /images/git.jpeg
date: 2022-01-11
tags:
  - Git
categories:
  - Git
---

## 前言

&nbsp;&nbsp;&nbsp;&nbsp;众所周知，分布式版本控制系统 git 是工作以后进行项目管理必不可少的工具，我将繁杂的命令进行了归类整理和总结，供大家参考学习，大家也可以在评论区说出自己对本文的意见，和小二一起交流学习，评论区有你们小二真是如虎添翼呀！！！
{% asset_img git.jpeg %}

@[TOC](文章の目录)

## 🏆 学习の目标

1.  能够掌握 Git 中基本命令的使用
2.  能够使用 Github 创建和维护远程仓库
3.  能够掌握 Git 分支及基本使用

## 一、🏆 Git 基础

### 1.1 安装并配置 Git

#### 1.1.1 在 Windows 中下载并安装 Git

{% asset_img gitOne1.gif %}
&nbsp;&nbsp;&nbsp;&nbsp;在开始使用 Git 管理项目的版本之前，需要将它安装到计算机上。可以使用浏览器访问如下的网址，根据自己
的操作系统，选择下载对应的 Git 安装包：

[git 安装包下载](https://git-scm.com/downloads)

#### 1.1.2 配置用户信息

&nbsp;&nbsp;&nbsp;&nbsp;安装完 Git 之后，要做的第一件事就是设置自己的用户名和邮件地址。因为通过 Git 对项目进行版本管理的时候，Git 需要使用这些基本信息，来记录是谁对项目进行了操作：
{% asset_img gitOne2.png %}
📣 注意：如果使用了--global 选项，那么该命令只能运行一次，即可永久生效。❗❗❗

#### 1.1.3 Git 的全局配置文件

&nbsp;&nbsp;&nbsp;&nbsp;通过 git config --global user.name 和 git config --global user.email 配置的用户名和邮箱地址，会被写入到 C:/Users/用户名文件夹/.gitconfig 文件中。这个文件是 Git 的全局配置文件，配置一次即可永久生效。可以使用记事本打开此文件，从而查看自己曾经对 Git 做了哪些全局性的配置。
{% asset_img gitOne3.png %}

#### 1.1.4 Git 检查配置信息

&nbsp;&nbsp;&nbsp;&nbsp;除了使用记事本查看全局的配置信息之外，还可以运行如下的终端命令，快速的查看 Git 的全局配置信息：

```javascript
//查看所有的全局配置项
git config --list --global
//查看指定的全局配置项
git config user.name
git config user.email
```

#### 1.1.5 Git 获取帮助信息

&nbsp;&nbsp;&nbsp;&nbsp;可以使用 git help <verb> 命令，无需联网即可在浏览器中打开帮助手册，例如：

```javascript
#打开git config帮助手册
git help config
```

&nbsp;&nbsp;&nbsp;&nbsp;如果不想查看完整的手册，那么可以用-h 选项获得更简明的“help”输出：

```javascript
#获取 git config 快速参考
git config -h
```

## 1.2 Git 的基本操作

### 1.2.1 获取 Git 仓库的两种方式

==① 将尚未进行版本控制的本地目录转换为 Git 仓库==

==② 从其它服务器克隆一个已存在的 Git 仓库==

以上两种方式都能够在自己的电脑上得到一个可用的 Git 仓库

#### 1.2.2 在现有目录中初始化仓库

&nbsp;&nbsp;&nbsp;&nbsp;如果自己有一个尚未进行版本控制的项目目录，想要用 Git 来控制它，需要执行如下两个步骤：

==① 在项目目录中，通过鼠标右键打开“Git Bash”==

==② 执行 git init 命令将当前的目录转化为 Git 仓库==
{% asset_img gitOne4.gif %}
git init 命令会创建一个名为.git 的隐藏目录，这个.git 目录就是当前项目的 Git 仓库，里面包含了初始的必要文件，这些文件是 Git 仓库的必要组成部分。

#### 1.2.3 检查文件状态

可以使用 git status 命令查看文件处于什么状态，例如：
{% asset_img gitOne5.gif %}
`在状态报告中可以看到新建的 文件出现在Untracked files（未跟踪的文件）下面。
未跟踪的文件意味着Git 在之前的快照（提交）中没有这些文件；Git 不会自动将之纳入跟踪范围，除非明确地告诉它“我需要使用Git 跟踪管理该文件”。`

#### 1.2.4 以精简的方式显示文件状态

&nbsp;&nbsp;&nbsp;&nbsp;使用 git status 输出的状态报告很详细，但有些繁琐。如果希望以精简的方式显示文件的状态，可以使用如下两条完全等价的命令，其中-s 是--short 的简写形式：
{% asset_img gitOne6.png %}

#### 1.2.5 跟踪新文件

&nbsp;&nbsp;&nbsp;&nbsp;使用命令 git add 开始跟踪一个文件。所以，要跟踪 index.html 文件，运行如下的命令即可：
{% asset_img gitOne7.png %}
此时再运行 git status 命令，会看到文件在 Changes to be committed 这行的下面，说明已被跟踪，并处于暂存状态：
{% asset_img gitOne8.png %}
以精简的方式获取状态：
{% asset_img gitOne9.png %}

#### 1.2.6 提交更新

&nbsp;&nbsp;&nbsp;&nbsp;现在暂存区中有一个 index.html 文件等待被提交到 Git 仓库中进行保存。可以执行 git commit 命令进行提交,其中-m 选项后面是本次的提交消息，用来对提交的内容做进一步的描述：
{% asset_img gitOne10.png %}
**提交成功之后，会显示如下的信息：**
{% asset_img gitOne11.png %}
**再次检查文件状态**
{% asset_img gitOne12.png %}

#### 1.2.7 对已提交的文件进行修改

&nbsp;&nbsp;&nbsp;&nbsp;目前，index.html 文件已经被 Git 跟踪，并且工作区和 Git 仓库中的 index.html 文件内容保持一致。当我们修改了工作区中 index.html 的内容之后，再次运行 git status 和 git status -s 命令，会看到如下的内容：
{% asset_img gitOne13.png %}
&nbsp;&nbsp;&nbsp;&nbsp;文件 index.html 出现 Changes not staged for commit 这行下面，说明已跟踪文件的内容发生了变化，但还没有放到暂存区。
`注意：修改过的、没有放入暂存区的文件前面有红色的M 标记。`

#### 1.2.8 暂存已修改的文件

&nbsp;&nbsp;&nbsp;&nbsp;目前，工作区中的 index.html 文件已被修改，如果要暂存这次修改，需要再次运行 git add 命令，这个命令是个多功能的命令，主要有如下 3 个功效：
==① 可以用它开始跟踪新文件==
==② 把已跟踪的、且已修改的文件放到暂存区==
==③ 把有冲突的文件标记为已解决状态==
{% asset_img gitOne14.png %}

#### 1.2.9 提交暂存的文件

&nbsp;&nbsp;&nbsp;&nbsp;再次运行 git commit -m "提交消息" 命令，即可将暂存区中记录的 index.html 的快照，提交到 Git 仓库中进行保存：
{% asset_img gitOne15.png %}
{% asset_img gitOne16.png %}

#### 1.2.10 撤销文件的修改

&nbsp;&nbsp;&nbsp;&nbsp;撤销对文件的修改指的是：把对工作区中对应文件的修改，还原成 Git 仓库中所保存的版本。
`操作的结果：所有的修改会丢失，且无法恢复！危险性比较高，请慎重操作！`
{% asset_img gitOne17.png %}
`撤销操作的本质：用Git 仓库中保存的文件，覆盖工作区中指定的文件。`

#### 1.2.11 向暂存区中一次性添加多个文件

&nbsp;&nbsp;&nbsp;&nbsp;如果需要被暂存的文件个数比较多，可以使用如下的命令，一次性将所有的新增和修改过的文件加入暂存区：
{% asset_img gitOne18.png %}
`注意：今后在项目开发中，会经常使用这个命令，将新增和修改过后的文件加入暂存区。`

#### 1.2.12 取消暂存的文件

&nbsp;&nbsp;&nbsp;&nbsp;如果需要从暂存区中移除对应的文件，可以使用如下的命令：
{% asset_img gitOne19.png %}

#### 1.2.13 跳过使用暂存区

&nbsp;&nbsp;&nbsp;&nbsp;Git 标准的工作流程是==工作区==→ ==暂存区==→ ==Git 仓库==，但有时候这么做略显繁琐，此时可以跳过暂存区，直接将工作区中的修改提交到 Git 仓库，这时候 Git 工作的流程简化为了工作区 → Git 仓库。
Git 提供了一个跳过使用暂存区域的方式，只要在提交的时候，给 git commit 加上-a 选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 git add 步骤：
{% asset_img gitOne20.png %}

#### 1.2.14 移除文件

&nbsp;&nbsp;&nbsp;&nbsp;从 Git 仓库中移除文件的方式有两种：
① 从 Git 仓库和工作区中同时移除对应的文件
② 只从 Git 仓库中移除指定的文件，但保留工作区中对应的文件；
{% asset_img gitOne21.png %}

#### 1.2.15 忽略文件

&nbsp;&nbsp;&nbsp;&nbsp;一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表。在这种情况下，我们可以创建一个名为.gitignore 的配置文件，列出要忽略的文件的匹配模式。
文件.gitignore 的格式规范如下：
① 以# 开头的是注释
② 以/结尾的是目录
③ 以/开头防止递归
④ 以!开头表示取反
⑤ 可以使用 glob 模式进行文件和文件夹的匹配（glob 指简化了的正则表达式）

#### 1.2.16 glob 模式

所谓的 glob 模式是指简化了的正则表达式：
① 星号\* 匹配零个或多个任意字符
②[abc] 匹配任何一个列在方括号中的字符（此案例匹配一个 a 或匹配一个 b 或匹配一个 c）
③ 问号?只匹配一个任意字符
④ 在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如[0-9] 表示匹配
所有 0 到 9 的数字）
⑤ 两个星号** 表示匹配任意中间目录（比如 a/**/z 可以匹配 a/z 、a/b/z 或 a/b/c/z 等）

#### 1.2.17 .gitignore 文件的例子

{% asset_img gitOne22.png %}

#### 1.2.18 查看提交历史

如果希望回顾项目的提交历史，可以使用 git log 这个简单且有效的命令。
{% asset_img gitOne23.png %}

#### 1.2.19 回退到指定版本

{% asset_img gitOne24.png %}

## 🥇 知识の总结

总结：
① 初始化 Git 仓库的命令
<font color=red>git init </font>
② 查看文件状态的命令
<font color=red>git status 或 git status -s</font>
③ 一次性将文件加入暂存区的命令
<font color=red>git add .</font>
④ 将暂存区的文件提交到 Git 仓库的命令
$\textcolor{red} {git commit -m "提交消息"}$
📣 注意：项目中经常使用以上基本操作请小伙伴牢记哦 ❗❗❗

## 🥇 推荐の内容

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞

学会项目管理需还需要掌握 Github、git 分支相关知识，我会陆续为大家整理 Github 和 Git 分支相关知识，大家敬请期待哦！！！
