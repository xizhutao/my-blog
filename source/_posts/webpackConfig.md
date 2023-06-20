---
title: 从零搭建webpack
description: 本篇文章将从基础配置入手，带大家了解webpack的核心；
headimg: /images/webpack.jpeg
date: 2022-03-22
tags:
  - Webpack
categories:
  - Webpack
---

## 前言

本质上，webpack 是一个用于现代 JavaScript 应用程序的 静态模块打包工具。当 webpack 处理应用程序时，它会在内部从一个或多个入口点构建一个 依赖图(dependency graph)，然后将你项目中所需的每一个模块组合成一个或多个 bundles，它们均为静态资源，用于展示你的内容，可想而知 webpack 的重要性。然而由于 vuecli 的出现，我们不再把更多的重心放到 webpack 的配置，导致对 webpack 的配置以及优化逐渐失去了自己的认识，本篇文章将从基础配置入手，带大家了解 webpack 的核心；

## 🥇 准备

首先我们需要在项目的根目录创建 webpack.config.js 文件用于 webpack 的配置，Webpack 是基于 Node.js 运行的，所以采用 Common.js 模块化规范；其五大核心为：entry、output、module、plugins、mode；代码如下所示：

```javascript
module.exports = {
  // 入口
  entry: '',
  // 输出
  output: {},
  // 加载器
  module: {
    rules: []
  },
  // 插件
  plugins: [],
  // 模式
  mode: ''
}
```

## 🥇 开发模式的配置

开发模式顾名思义就是我们开发代码时使用的模式。这个模式下我们主要做两件事：==1、编译代码，使浏览器能识别运行开发时我们有样式资源、字体图标、图片资源、html 资源等，webpack 默认都不能处理这些资源，所以我们要加载配置来编译这些资源代码质量检查，2、树立代码规范提前检查代码的一些隐患，让代码运行时能更加健壮。提前检查代码规范和格式，统一团队编码风格，让代码更优雅美观。==

### 1、处理 css 样式资源

由于 Webpack 是不能解析样式资源的因此我们需要借助 loader 去帮组 webpack 解析样式资源；官方文档有对应的 loader 模块，我们可以参考官方文档进行 loader 的配置；链接如下：
[webpack----------Loader 地址](https://webpack.docschina.org/loaders/)
第一步下载 loader，代码如下所示：

```javascript
npm i css-loader style-loader -D
```

第二步在配置文件中配置，代码如下所示：

```javascript
const path = require('path')

module.exports = {
  entry: './src/main.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'main.js'
  },
  module: {
    rules: [
      {
        // 用来匹配 .css 结尾的文件
        test: /\.css$/,
        // use 数组里面 Loader 执行顺序是从右到左
        use: ['style-loader', 'css-loader']
      }
    ]
  },
  plugins: [],
  mode: 'development'
}
```

对 less、sass、styl 资源的处理和 css 资源的处理雷同，不再累述，查看官方文档进行配置即可;

### 2、处理图片资源

在 Webapack4 我们需要对一个两个 loader 对图片资源处理图片，而 webpack5 已经将处理图片的 loader 内置处理了，现在只需要进行简单配置就能处理图片资源，配置代码如下所示：

```javascript
const path = require('path')

module.exports = {
  entry: './src/main.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'main.js'
  },
  module: {
    rules: [
      //...........处理图片资源的配置
      {
        test: /\.(png|jpe?g|gif|webp)$/,
        type: 'asset'
      }
    ]
  },
  plugins: [],
  mode: 'development'
}
```

#### 2.1 对图片资源进行优化

项目中肯定会存在一定的图片资源，为了减少向后端发请求的次数，我们配置了小于 10kb 的图片转 base64 格式的配置项；

```javascript
 {
        test: /\.(png|jpe?g|gif|webp)$/,
        type: "asset",
        parser: {
          dataUrlCondition: {
            maxSize: 10 * 1024 // 小于10kb的图片会被base64处理
          }
        }
      },
```

### 3、修改输出资源的名称和路径

输出 js 文件名称和路径的配置在如下 output 中配置：

```javascript
output: {
    path: path.resolve(__dirname, "dist"),
    filename: "static/js/main.js", // 将 js 文件输出到 static/js 目录中
  },
```

输出图片的名称和路径的配置如下：

```javascript
generator: {
          // 将图片文件输出到 static/imgs 目录中
          // 将图片文件命名 [hash:8][ext][query]
          // [hash:8]: hash值取8位
          // [ext]: 使用之前的文件扩展名
          // [query]: 添加之前的query参数
          filename: "static/imgs/[hash:8][ext][query]",
        },
```

### 4、自动清空上次打包的配置

由于每次在打包资源之前我们都需要手动删除 dist 目录下的打包文件，非常麻烦，因此需要配置每次打包之前先清除 dist 目录下的资源再进行打包，配置代码如下：

```javascript
output: {
    path: path.resolve(__dirname, "dist"),
    filename: "static/js/main.js",
    clean: true, // 自动将上次打包目录资源清空
  },
```

### 5、字体图标资源的处理

对于字体图标的处理和图片资源的处理类似，因为 webpack 内置的有 loader 解析，我们只需要简单的配置即可，配置代码如下：

```javascript
{
        test: /\.(ttf|woff2?)$/,
        type: "asset/resource",
        generator: {
          filename: "static/media/[hash:8][ext][query]",
        },
      },
```

注意：📣 type: "asset/resource"和 type: "asset"的区别：
type: "asset/resource" 相当于 file-loader, 将文件转化成 Webpack 能识别的资源，其他不做处理
type: "asset" 相当于 url-loader, 将文件转化成 Webpack 能识别的资源，同时小于某个大小的资源会处理成 data URI 形式 ❗❗❗

### 6、处理其它资源

开发中可能还存在一些其他资源，如音视频等，我们在字体图标的配置中也一起处理了，代码如下：

```javascript
{
        test: /\.(ttf|woff2?|map4|map3|avi)$/,
        type: "asset/resource",
        generator: {
          filename: "static/media/[hash:8][ext][query]",
        },
      },
```

### 7、处理 js 资源

#### 7.1 Eslint

在团队的开发中对代码的格式是有严格的要求，因此为了团队代码的规范，我们需要配置 eslint 去保证开发的代码格式的一致性；首先我们需要在项目的根目录下创建一个.eslintrc.js 文件，在里面配置如下三项规则：

```javascript
module.exports = {
  // 解析选项
  parserOptions: {},
  // 具体检查规则
  rules: {},
  // 继承其他规则
  extends: []
  // ...
  // 其他规则详见：https://eslint.bootcss.com/docs/user-guide/configuring
}
```

1、parserOptions 选项配置如下：

```javascript
parserOptions: {
  ecmaVersion: 6, // ES 语法版本
  sourceType: "module", // ES 模块化
  ecmaFeatures: { // ES 其他特性
    jsx: true // 如果是 React 项目，就需要开启 jsx 语法
  }
}
```

2、rules 的配置规则：
"off" 或 0 - 关闭规则
"warn" 或 1 - 开启规则，使用警告级别的错误：warn (不会导致程序退出)
"error" 或 2 - 开启规则，使用错误级别的错误：error (当被触发的时候，程序会退出)；
其配置如下所示：

```javascript
rules: {
  semi: "error", // 禁止使用分号
  'array-callback-return': 'warn', // 强制数组方法的回调函数中有 return 语句，否则警告
  'default-case': [
    'warn', // 要求 switch 语句中有 default 分支，否则警告
    { commentPattern: '^no default$' } // 允许在最后注释 no default, 就不会有警告了
  ],
  eqeqeq: [
    'warn', // 强制使用 === 和 !==，否则警告
    'smart' // https://eslint.bootcss.com/docs/rules/eqeqeq#smart 除了少数情况下不会有警告
  ],
}
```

更多规则可以参考如下官方文档：[ESLint 官方](https://eslint.bootcss.com/docs/rules/)
3、在 webpack 中的使用：

```javascript
module.exports = {
  // 继承 Eslint 规则
  extends: ['eslint:recommended'],
  env: {
    node: true, // 启用node中全局变量
    browser: true // 启用浏览器中全局变量
  },
  parserOptions: {
    ecmaVersion: 6,
    sourceType: 'module'
  },
  rules: {
    'no-var': 2 // 不能使用 var 定义变量
  }
}
```

与此同时我们可以在 webpackconfig.js 中配置插件指定检查文件的目录：

```javascript
plugins: [
    new ESLintWebpackPlugin({
      // 指定检查文件的根目录
      context: path.resolve(__dirname, "src"),
    }),
  ],
```

配置插件之前需要下载插件并且引入，文章以下的内容不再累述下包引入的过程；

##### 7.1.1 VS Code 的 Eslint 插件

打开 VSCode，下载 Eslint 插件，即可不用编译就能看到错误，可以提前解决但是此时就会对项目所有文件默认进行 Eslint 检查了，我们 dist 目录下的打包后文件就会报错。但是我们只需要检查 src 下面的文件，不需要检查 dist 下面的文件。所以可以使用 Eslint 忽略文件解决。在项目根目录新建.eslintignore 文件:

```javascript
# 忽略dist目录下所有文件
dist
```

#### 7.2 Babel

Babel 为 javascript 编译器，主要用于将 ES6 语法编写的代码转换为向后兼容的 JavaScript 语法，以便能够运行在当前和旧版本的浏览器或其他环境中；我们需要先在根目录下创建一个 babel.config.js 文件；
在 webpack 中的使用：
1、下载包：

```javascript
npm i babel-loader @babel/core @babel/preset-env -D
```

2、定义 babel.config.js 文件

```javascript
module.exports = {
  presets: ['@babel/preset-env']
}
```

3、webconfig.js 中配置如下代码：

```javascript
 {
        test: /\.js$/,
        exclude: /node_modules/, // 排除node_modules代码不编译
        loader: "babel-loader",
      },
```

以上代码配置排除 node_modules 文件，提高打包的速度；

### 8、处理 html 资源

1、下载包

```javascript
npm i html-webpack-plugin -D
```

2、在插件模块配置如下代码：

```javascript
 new HtmlWebpackPlugin({
      // 以 public/index.html 为模板创建文件
      // 新的html文件有两个特点：1. 内容和源文件一致 2. 自动引入打包生成的js等资源
      template: path.resolve(__dirname, "public/index.html"),
    }),
```

## 🥇 开发服务器&自动化

每次写完代码都需要手动输入指令才能编译代码，太麻烦了，我们希望一切自动化

### 1、下载包

```javascript
npm i webpack-dev-server -D
```

### 2、配置开发服务器

```javascript
// 开发服务器
  devServer: {
    host: "localhost", // 启动服务器域名
    port: "3000", // 启动服务器端口号
    open: true, // 是否自动打开浏览器
  },
```

## 🥇 生产模式配置

生产模式是开发完成代码后，我们需要得到代码将来部署上线。这个模式下我们主要对代码进行优化，让其运行性能更好。优化主要从两个角度出发:
1、优化代码运行性能
2、优化代码打包速度

### 1、准备

在根目录下创建 config 文件夹用来放置开发环境和生产环境的配置 webpack.dev.js、webpack.prod.js

### 2、修改 webpack.dev.js 文件

因为文件目录变了，所以所有绝对路径需要回退一层目录才能找到对应的文件
完整代码如下：

```javascript
const path = require('path')
const ESLintWebpackPlugin = require('eslint-webpack-plugin')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  entry: './src/main.js',
  output: {
    path: undefined, // 开发模式没有输出，不需要指定输出目录
    filename: 'static/js/main.js' // 将 js 文件输出到 static/js 目录中
    // clean: true, // 开发模式没有输出，不需要清空输出结果
  },
  module: {
    rules: [
      {
        // 用来匹配 .css 结尾的文件
        test: /\.css$/,
        // use 数组里面 Loader 执行顺序是从右到左
        use: ['style-loader', 'css-loader']
      },
      {
        test: /\.less$/,
        use: ['style-loader', 'css-loader', 'less-loader']
      },
      {
        test: /\.s[ac]ss$/,
        use: ['style-loader', 'css-loader', 'sass-loader']
      },
      {
        test: /\.styl$/,
        use: ['style-loader', 'css-loader', 'stylus-loader']
      },
      {
        test: /\.(png|jpe?g|gif|webp)$/,
        type: 'asset',
        parser: {
          dataUrlCondition: {
            maxSize: 10 * 1024 // 小于10kb的图片会被base64处理
          }
        },
        generator: {
          // 将图片文件输出到 static/imgs 目录中
          // 将图片文件命名 [hash:8][ext][query]
          // [hash:8]: hash值取8位
          // [ext]: 使用之前的文件扩展名
          // [query]: 添加之前的query参数
          filename: 'static/imgs/[hash:8][ext][query]'
        }
      },
      {
        test: /\.(ttf|woff2?)$/,
        type: 'asset/resource',
        generator: {
          filename: 'static/media/[hash:8][ext][query]'
        }
      },
      {
        test: /\.js$/,
        exclude: /node_modules/, // 排除node_modules代码不编译
        loader: 'babel-loader'
      }
    ]
  },
  plugins: [
    new ESLintWebpackPlugin({
      // 指定检查文件的根目录
      context: path.resolve(__dirname, '../src')
    }),
    new HtmlWebpackPlugin({
      // 以 public/index.html 为模板创建文件
      // 新的html文件有两个特点：1. 内容和源文件一致 2. 自动引入打包生成的js等资源
      template: path.resolve(__dirname, '../public/index.html')
    })
  ],
  // 其他省略
  devServer: {
    host: 'localhost', // 启动服务器域名
    port: '3000', // 启动服务器端口号
    open: true // 是否自动打开浏览器
  },
  mode: 'development'
}
```

运行开发模式的指令：`npx webpack serve --config ./config/webpack.dev.js`

### 3、修改 webpack.prod.js 文件

修改后的代码如下：

```javascript
const path = require('path')
const ESLintWebpackPlugin = require('eslint-webpack-plugin')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  entry: './src/main.js',
  output: {
    path: path.resolve(__dirname, '../dist'), // 生产模式需要输出
    filename: 'static/js/main.js', // 将 js 文件输出到 static/js 目录中
    clean: true
  },
  module: {
    rules: [
      {
        // 用来匹配 .css 结尾的文件
        test: /\.css$/,
        // use 数组里面 Loader 执行顺序是从右到左
        use: ['style-loader', 'css-loader']
      },
      {
        test: /\.less$/,
        use: ['style-loader', 'css-loader', 'less-loader']
      },
      {
        test: /\.s[ac]ss$/,
        use: ['style-loader', 'css-loader', 'sass-loader']
      },
      {
        test: /\.styl$/,
        use: ['style-loader', 'css-loader', 'stylus-loader']
      },
      {
        test: /\.(png|jpe?g|gif|webp)$/,
        type: 'asset',
        parser: {
          dataUrlCondition: {
            maxSize: 10 * 1024 // 小于10kb的图片会被base64处理
          }
        },
        generator: {
          // 将图片文件输出到 static/imgs 目录中
          // 将图片文件命名 [hash:8][ext][query]
          // [hash:8]: hash值取8位
          // [ext]: 使用之前的文件扩展名
          // [query]: 添加之前的query参数
          filename: 'static/imgs/[hash:8][ext][query]'
        }
      },
      {
        test: /\.(ttf|woff2?)$/,
        type: 'asset/resource',
        generator: {
          filename: 'static/media/[hash:8][ext][query]'
        }
      },
      {
        test: /\.js$/,
        exclude: /node_modules/, // 排除node_modules代码不编译
        loader: 'babel-loader'
      }
    ]
  },
  plugins: [
    new ESLintWebpackPlugin({
      // 指定检查文件的根目录
      context: path.resolve(__dirname, '../src')
    }),
    new HtmlWebpackPlugin({
      // 以 public/index.html 为模板创建文件
      // 新的html文件有两个特点：1. 内容和源文件一致 2. 自动引入打包生成的js等资源
      template: path.resolve(__dirname, '../public/index.html')
    })
  ],
  // devServer: {
  //   host: "localhost", // 启动服务器域名
  //   port: "3000", // 启动服务器端口号
  //   open: true, // 是否自动打开浏览器
  // },
  mode: 'production'
}
```

运行如下指令：`npx webpack --config ./config/webpack.prod.js`

### 4、修改 package.json 文件

修改 script 使得指令更简单：

```javascript
// package.json
{
  // 其他省略
  "scripts": {
    "start": "npm run dev",
    "dev": "npx webpack serve --config ./config/webpack.dev.js",
    "build": "npx webpack --config ./config/webpack.prod.js"
  }
}
```

## 🥇 Css 的处理

### 1、提取 css 成单独文件

Css 文件目前被打包到 js 文件中，当 js 文件加载时，会创建一个 style 标签来生成样式这样对于网站来说，会出现闪屏现象，用户体验不好我们应该是单独的 Css 文件，通过 link 标签加载性能才好；
1、下载包：

```javascript
npm i mini-css-extract-plugin -D
```

2、webpack.prod.js 中添加如下代码：
在 use 数组中添加 MiniCssExtractPlugin.loader

```javascript
{
        // 用来匹配 .css 结尾的文件
        test: /\.css$/,
        // use 数组里面 Loader 执行顺序是从右到左
        use: [MiniCssExtractPlugin.loader, "css-loader"],
      },
      {
        test: /\.less$/,
        use: [MiniCssExtractPlugin.loader, "css-loader", "less-loader"],
      },
      {
        test: /\.s[ac]ss$/,
        use: [MiniCssExtractPlugin.loader, "css-loader", "sass-loader"],
      },
      {
        test: /\.styl$/,
        use: [MiniCssExtractPlugin.loader, "css-loader", "stylus-loader"],
      },
```

在 plugin 插件中配置如下代码：

```javascript
// 提取css成单独文件
    new MiniCssExtractPlugin({
      // 定义输出文件名和目录
      filename: "static/css/main.css",
    }),
```

### 2、css 兼容性处理

1、下载包：

```javascript
npm i postcss-loader postcss postcss-preset-env -D
```

2、在 css-loader 下添加如下配置：

```javascript
{
            loader: "postcss-loader",
            options: {
              postcssOptions: {
                plugins: [
                  "postcss-preset-env", // 能解决大多数样式兼容性问题
                ],
              },
            },
          },
```

3、控制兼容性
我们可以在 package.json 文件中添加 browserslist 来控制样式的兼容性做到什么程度。

```javascript
{
  // 其他省略
  "browserslist": ["last 2 version", "> 1%", "not dead"]
}
```

### 3、css 压缩

1、下载包：

```javascript
npm i css-minimizer-webpack-plugin -D
```

2、在 plugins 模块配置如下代码：

```javascript
 // css压缩
    new CssMinimizerPlugin(),
```

## 🥇 html 压缩

默认生产模式已经开启了：html 压缩和 js 压缩不需要额外进行配置

## 🥇 总结

本章文章我们学会了 Webpack 基本使用，掌握了以下功能：

1、两种开发模式
1.1 开发模式：代码能编译自动化运行
1.2 生产模式：代码编译优化输出
2、Webpack 基本功能
2.1 开发模式：可以编译 ES Module 语法
2.2 生产模式：可以编译 ES Module 语法，压缩 js 代码
3、Webpack 配置文件 5 个核心概念
entry
output
loader
plugins
mode
devServer 配置
Webpack 脚本指令用法
webpack 直接打包输出
webpack serve 启动开发服务器，内存编译打包没有输出

## 写在最后

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
