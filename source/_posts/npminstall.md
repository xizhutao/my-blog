---
title: npm install --save、npm install --save-dev
description:
  在进行项目开发时，有许多新手小伙伴不理解一些常见的用npm安装包的命令，让我大跌眼镜，下面将详细讲解以下四个命令的异同点；
  1. npm install --save moduleName
  2. npm install --save-dev moduleName
  3. npm install -g moduleName
  4. npm install moduleName
headimg: /images/node.jpeg
date: 2022-05-20
tags:
  - Node
categories:
  - Node
---

## 背景

在进行项目开发时，有许多新手小伙伴不理解一些常见的用 npm 安装包的命令，让我大跌眼镜，下面将详细讲解以下四个命令的异同点；

1.  npm install --save moduleName
2.  npm install --save-dev moduleName
3.  npm install -g moduleName
4.  npm install moduleName

## 一、npm install --save moduleName 命令

1. 安装模块到项目 node_modules 目录下。
2. 会将模块依赖写入 dependencies 节点。
3. 运行 npm install 初始化项目时，会将模块下载到项目目录下。
4. 运行 npm install --production 或者注明 NODE_ENV 变量值为 production 时，会自动下载模块到 node_modules 目录中。

## 二、npm install --save-dev moduleName 命令

1. 安装模块到项目 node_modules 目录下。
2. 会将模块依赖写入 dependencies 节点。
3. 运行 npm install 初始化项目时，会将模块下载到项目目录下。
4. 运行 npm install --production 或者注明 NODE_ENV 变量值为 production 时，会自动下载模块到 node_modules 目录中。

## 三、npm install -g moduleName 命令

1. 安装模块到全局，不会在项目 node_modules 目录中保存模块包。
2. 不会将模块依赖写入 devDependencies 或 dependencies 节点。
3. 运行 npm install 初始化项目时不会下载模块。

## 四、npm install moduleName 命令

1. 安装模块到项目 node_modules 目录下。
2. 不会将模块依赖写入 devDependencies 或 dependencies 节点。
3. 运行 npm install 初始化项目时不会下载模块。

## 写在最后

devDependencies 节点下的模块是我们在开发时需要用的，比如项目中使用的 gulp ，压缩 css、js 的模块。这些模块在我们的项目部署后是不需要的，所以我们可以使用 -save-dev 的形式安装。像 express 这些模块是项目运行必备的，应该安装在 dependencies 节点下，所以我们应该使用 -save 的形式安装。

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
