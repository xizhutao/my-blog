---
title: git rebase 配合 git cherry-pick 使用
description: git rebase能够合并多个提交，git cherry-pick 能根据commitId将提交拷贝到主分支上；下面我将依次带大家学习这两个好用的命令；
headimg: /images/git.jpeg
date: 2021-12-09
tags:
  - Git
categories:
  - Git
---

## 前言

{% asset_img git.jpeg %}
相信大家在开发中一定遇到过一下我说是两个问题：
`1、实现一个功能但是提交了多次，导致commit的log很臃肿，不够清晰；`
`2、在合并分支时，有时候我们只想合并dev分支上的部分commit，而不是全部合并；`
若想解决以上两个问题，git rebase 和 git cherry-pick 便是最好的选择，git rebase 能够合并多个提交，git cherry-pick 能根据 commitId 将提交拷贝到主分支上；下面我将依次带大家学习这两个好用的命令；

## 1、 git rebase

第一步：假如你的开发分支是 dev，首先先切到你自己的分支上,然后修改 3 次代码，提交 3 次，git log 后如下所示；
{% asset_img rebase1.png %}
第二步：若想和并以上 3 个分支我们可以执行如下命令进行分支编辑；

```javascript
git rebase -i  [startpoint]  [endpoint]
//或者
git rebase -i HEAD~3
```

其中-i 的意思是--interactive，即弹出交互式的界面让用户编辑完成合并操作，[startpoint] [endpoint]则指定了一个编辑区间，均为 commitId，如果不指定[endpoint]，则该区间的终点默认是当前分支 HEAD 所指向的 commit(注：该区间指定的是一个前开后闭的区间)。
执行以上命令后，进入了分支编辑页面如下：
{% asset_img rebase2.png %}
以上#号注释的是指令说明，其释义如下：
pick：保留该 commit（缩写:p）
reword：保留该 commit，但我需要修改该 commit 的注释（缩写:r）
edit：保留该 commit, 但我要停下来修改该提交(不仅仅修改注释)（缩写:e）
squash：将该 commit 和前一个 commit 合并（缩写:s）
fixup：将该 commit 和前一个 commit 合并，但我不要保留该提交的注释信息（缩写:f）
exec：执行 shell 命令（缩写:x）
drop：我要丢弃该 commit（缩写:d）
按 i 进入编辑状态，将下面的两个 pick 改成 s，如下图所示：
{% asset_img rebase3.png %}
按 esc -> 冒号 -> wq （退出并保存）
此时进入了提交信息的编辑页面如下所示：
{% asset_img rebase4.png %}
可以使用 dd 删除提交信息保留一个即可,继续按 esc -> 冒号 -> wq （退出并保存）
此时执行 git log 看到如下图所示，只有 commit1 了
{% asset_img rebase5.png %}

## 2、 git cherry-pick

第一节已经讲解了 git rebase，本节接上一节的最后教大家如何使用 git cherry-pick 在 dev 分支合并了多个提交后，此时大家一定会首先想到使用 git merge 合并到主分支，实际上假如我只想将已经被合并成 commit1 的最近的三次提交合并到主分支，此时即可以使用 git cherry-pick；
执行步骤如下：
1、首先切换到 master 分支；
2、然后执行 git cherry-pick commitId
以上 commitId 是在 dev 分支上你要拷贝到主分支的 commitId；执行完毕即可以通过 git log 看到主分支上多了 commit1 的提交；

## 写在最后

git rebase 和 git cherry-pick 的使用能大大提高代码提交的规范和 git 日志的可读性；对于 git cherry-pick,网上也有些不同的声音，链接如下，欢迎大家留言交流：
[Stop cherry-picking, start merging, Part 1: The merge conflict
](https://devblogs.microsoft.com/oldnewthing/20180312-00/?p=98215)

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
