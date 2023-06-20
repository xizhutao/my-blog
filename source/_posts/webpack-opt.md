---
title: Webpack打包优化
description: 此篇文章带大家学习Webpack的优化，让我们的代码在编译/运行时性能更好~
headimg: /images/webpack.jpeg
date: 2022-04-22
tags:
  - Webpack
categories:
  - Webpack
---

## 前言

上一篇文章详细讲解了 Webpack 的基础配置，这篇文章将在上一篇的基础上讲解 Webpack 的高级配置，带大家学习 Webpack 的优化，让我们的代码在编译/运行时性能更好~

## 1、提升开发体验

&nbsp;&nbsp;&nbsp;&nbsp;开发时我们运行的代码是经过 webpack 编译后的，所有 css 和 js 合并成了一个文件，并且多了其他代码。此时如果代码运行出错那么提示代码错误位置我们是看不懂的。一旦将来开发代码文件很多，那么很难去发现错误出现在哪里。所以我们需要更加准确的错误提示，来帮助我们更好的开发代码。
&nbsp;&nbsp;&nbsp;&nbsp;SourceMap（源代码映射）是一个用来生成源代码与构建后代码一一映射的文件的方案。它会生成一个 xxx.map 文件，里面包含源代码和构建后代码每一行、每一列的映射关系。当构建后代码出错了，会通过 xxx.map 文件，从构建后代码出错位置找到映射后源代码出错位置，从而让浏览器提示源代码文件出错位置，帮助我们更快的找到错误根源;
1、开发模式下的配置：

```javascript
module.exports = {
  // 其他省略
  mode: 'development',
  devtool: 'cheap-module-source-map'
}
```

开发模式下打包速度快，只包含行映射不包含列映射；
2、生产模式下的配置：

```javascript
module.exports = {
  // 其他省略
  mode: 'production',
  devtool: 'source-map'
}
```

## 2、提升打包构建速度

### 2.1 HotModuleReplacement

开发时我们修改了其中一个模块代码，Webpack 默认会将所有模块全部重新打包编译，速度很慢。所以我们需要做到修改某个模块代码，就只有这个模块代码需要重新打包编译，其他模块不变，这样打包速度就能很快;HotModuleReplacement（HMR/热模块替换）：在程序运行中，替换、添加或删除模块，而无需重新加载整个页面。
1、基本配置：

```javascript
module.exports = {
  // 其他省略
  devServer: {
    host: 'localhost', // 启动服务器域名
    port: '3000', // 启动服务器端口号
    open: true, // 是否自动打开浏览器
    hot: true // 开启HMR功能（只能用于开发环境，生产环境不需要了）
  }
}
```

开启热模替换后，实际开发配合[Vue-loader](https://github.com/vuejs/vue-loader) 插件进行配置

### 2.2 oneOf

打包时每个文件都会经过所有 loader 处理，虽然因为 test 正则原因实际没有处理上，但是都要过一遍，比较慢。如果想要提升效率，就要只匹配一次，即匹配上了就不再往下匹配；
配置代码如下:

```javascript
 module: {
    rules: [
      {
        oneOf: [
          {
            // 用来匹配 .css 结尾的文件
            test: /\.css$/,
            // use 数组里面 Loader 执行顺序是从右到左
            use: ["style-loader", "css-loader"],
          },
          {
            test: /\.less$/,
            use: ["style-loader", "css-loader", "less-loader"],
          },
          {
            test: /\.s[ac]ss$/,
            use: ["style-loader", "css-loader", "sass-loader"],
          },
          {
            test: /\.styl$/,
            use: ["style-loader", "css-loader", "stylus-loader"],
          },
          {
            test: /\.(png|jpe?g|gif|webp)$/,
            type: "asset",
            parser: {
              dataUrlCondition: {
                maxSize: 10 * 1024, // 小于10kb的图片会被base64处理
              },
            },
            generator: {
              // 将图片文件输出到 static/imgs 目录中
              // 将图片文件命名 [hash:8][ext][query]
              // [hash:8]: hash值取8位
              // [ext]: 使用之前的文件扩展名
              // [query]: 添加之前的query参数
              filename: "static/imgs/[hash:8][ext][query]",
            },
          },
          {
            test: /\.(ttf|woff2?)$/,
            type: "asset/resource",
            generator: {
              filename: "static/media/[hash:8][ext][query]",
            },
          },
          {
            test: /\.js$/,
            exclude: /node_modules/, // 排除node_modules代码不编译
            loader: "babel-loader",
          },
        ],
      },
    ],
  },
```

### 2.3 Include/Exclude

开发时我们需要使用第三方的库或插件，所有文件都下载到 node_modules 中了。而这些文件是不需要编译可以直接使用的。所以我们在对 js 文件处理时，要排除 node_modules 下面的文件;
配置代码如下：
**babel 编译：**

```javascript
{
            test: /\.js$/,
            // exclude: /node_modules/, // 排除node_modules代码不编译
            include: path.resolve(__dirname, "../src"), // 也可以用包含
            loader: "babel-loader",
          },
```

**eslint 检查：**

```javascript
new ESLintWebpackPlugin({
      // 指定检查文件的根目录
      context: path.resolve(__dirname, "../src"),
      exclude: "node_modules", // 默认值
    }),
```

### 2.4 Cache

每次打包时 js 文件都要经过 Eslint 检查 和 Babel 编译，速度比较慢。我们可以缓存之前的 Eslint 检查 和 Babel 编译结果，这样第二次打包时速度就会更快了。
**js 编译**

```javascript
{
            test: /\.js$/,
            // exclude: /node_modules/, // 排除node_modules代码不编译
            include: path.resolve(__dirname, "../src"), // 也可以用包含
            loader: "babel-loader",
            options: {
              cacheDirectory: true, // 开启babel编译缓存
              cacheCompression: false, // 缓存文件不要压缩
            },
          },
```

**eslint 检查**

```javascript
new ESLintWebpackPlugin({
      // 指定检查文件的根目录
      context: path.resolve(__dirname, "../src"),
      exclude: "node_modules", // 默认值
      cache: true, // 开启缓存
      // 缓存目录
      cacheLocation: path.resolve(
        __dirname,
        "../node_modules/.cache/.eslintcache"
      ),
    }),
```

### 2.5 Thead

当项目越来越庞大时，打包速度越来越慢，甚至于需要一个下午才能打包出来代码。这个速度是比较慢的。我们想要继续提升打包速度，其实就是要提升 js 的打包速度，因为其他文件都比较少。而对 js 文件处理主要就是 eslint 、babel、Terser 三个工具，所以我们要提升它们的运行速度。我们可以开启多进程同时处理 js 文件，这样速度就比之前的单进程打包更快了。
1、获取 cpu 核数

```javascript
// nodejs核心模块，直接使用
const os = require('os')
// cpu核数
const threads = os.cpus().length
```

2、下载包

```javascript
npm i thread-loader -D
```

3、配置

```javascript
const os = require('os')
const path = require('path')
const ESLintWebpackPlugin = require('eslint-webpack-plugin')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin')
const TerserPlugin = require('terser-webpack-plugin')

// cpu核数
const threads = os.cpus().length

// 获取处理样式的Loaders
const getStyleLoaders = (preProcessor) => {
  return [
    MiniCssExtractPlugin.loader,
    'css-loader',
    {
      loader: 'postcss-loader',
      options: {
        postcssOptions: {
          plugins: [
            'postcss-preset-env' // 能解决大多数样式兼容性问题
          ]
        }
      }
    },
    preProcessor
  ].filter(Boolean)
}

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
        oneOf: [
          {
            // 用来匹配 .css 结尾的文件
            test: /\.css$/,
            // use 数组里面 Loader 执行顺序是从右到左
            use: getStyleLoaders()
          },
          {
            test: /\.less$/,
            use: getStyleLoaders('less-loader')
          },
          {
            test: /\.s[ac]ss$/,
            use: getStyleLoaders('sass-loader')
          },
          {
            test: /\.styl$/,
            use: getStyleLoaders('stylus-loader')
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
            // exclude: /node_modules/, // 排除node_modules代码不编译
            include: path.resolve(__dirname, '../src'), // 也可以用包含
            use: [
              {
                loader: 'thread-loader', // 开启多进程
                options: {
                  workers: threads // 数量
                }
              },
              {
                loader: 'babel-loader',
                options: {
                  cacheDirectory: true // 开启babel编译缓存
                }
              }
            ]
          }
        ]
      }
    ]
  },
  plugins: [
    new ESLintWebpackPlugin({
      // 指定检查文件的根目录
      context: path.resolve(__dirname, '../src'),
      exclude: 'node_modules', // 默认值
      cache: true, // 开启缓存
      // 缓存目录
      cacheLocation: path.resolve(__dirname, '../node_modules/.cache/.eslintcache'),
      threads // 开启多进程
    }),
    new HtmlWebpackPlugin({
      // 以 public/index.html 为模板创建文件
      // 新的html文件有两个特点：1. 内容和源文件一致 2. 自动引入打包生成的js等资源
      template: path.resolve(__dirname, '../public/index.html')
    }),
    // 提取css成单独文件
    new MiniCssExtractPlugin({
      // 定义输出文件名和目录
      filename: 'static/css/main.css'
    })
    // css压缩
    // new CssMinimizerPlugin(),
  ],
  optimization: {
    minimize: true,
    minimizer: [
      // css压缩也可以写到optimization.minimizer里面，效果一样的
      new CssMinimizerPlugin(),
      // 当生产模式会默认开启TerserPlugin，但是我们需要进行其他配置，就要重新写了
      new TerserPlugin({
        parallel: threads // 开启多进程
      })
    ]
  },
  // devServer: {
  //   host: "localhost", // 启动服务器域名
  //   port: "3000", // 启动服务器端口号
  //   open: true, // 是否自动打开浏览器
  // },
  mode: 'production',
  devtool: 'source-map'
}
```

注意 📣：==请仅在特别耗时的操作中使用，因为每个进程启动就有大约为 600ms 左右开销==。❗❗❗❗❗❗

## 3、减少代码体积

### 3.1 TreeShaking

&nbsp;&nbsp;&nbsp;&nbsp;开发时我们定义了一些工具函数库，或者引用第三方工具函数库或组件库。如果没有特殊处理的话我们打包时会引入整个库，但是实际上可能我们可能只用上极小部分的功能。这样将整个库都打包进来，体积就太大了。
&nbsp;&nbsp;&nbsp;&nbsp;Tree Shaking 是一个术语，通常用于描述移除 JavaScript 中的没有使用上的代码,Webpack 已经默认开启了这个功能，无需其他配置。

### 3.2 Babel

Babel 为编译的每个文件都插入了辅助代码，使代码体积过大！Babel 对一些公共方法使用了非常小的辅助代码，比如 \_extend。默认情况下会被添加到每一个需要它的文件中。你可以将这些辅助代码作为一个独立模块，来避免重复引入。
@babel/plugin-transform-runtime: 禁用了 Babel 自动对每个文件的 runtime 注入，而是引入 @babel/plugin-transform-runtime 并且使所有辅助代码从这里引用;
1、下载包

```javascript
npm i @babel/plugin-transform-runtime -D
```

2、配置

```javascript
 {
                loader: "babel-loader",
                options: {
                  cacheDirectory: true, // 开启babel编译缓存
                  cacheCompression: false, // 缓存文件不要压缩
                  plugins: ["@babel/plugin-transform-runtime"], // 减少代码体积
                },
              },
```

### 3.3 Image Minimizer

开发如果项目中引用了较多图片，那么图片体积会比较大，将来请求速度比较慢。我们可以对图片进行压缩，减少图片体积。
1、下载插件

```javascript
npm i image-minimizer-webpack-plugin imagemin -D
```

2、无损压缩下载如下包

```javascript
npm install imagemin-gifsicle imagemin-jpegtran imagemin-optipng imagemin-svgo -D
```

3、有损压缩

```javascript
npm install imagemin-gifsicle imagemin-mozjpeg imagemin-pngquant imagemin-svgo -D
```

4、以无损压缩进行示例配置

```javascript
// 压缩图片
      new ImageMinimizerPlugin({
        minimizer: {
          implementation: ImageMinimizerPlugin.imageminGenerate,
          options: {
            plugins: [
              ["gifsicle", { interlaced: true }],
              ["jpegtran", { progressive: true }],
              ["optipng", { optimizationLevel: 5 }],
              [
                "svgo",
                {
                  plugins: [
                    "preset-default",
                    "prefixIds",
                    {
                      name: "sortAttrs",
                      params: {
                        xmlnsOrder: "alphabetical",
                      },
                    },
                  ],
                },
              ],
            ],
          },
        },
      }),
```

注意：📣== 如果项目中图片都是在线链接，那么就不需要了。本地项目静态图片才需要进行压缩 ❗❗❗

## 4、优化代码运行性能

### 4.1 Code Split

&nbsp;&nbsp;&nbsp;&nbsp;打包代码时会将所有 js 文件打包到一个文件中，体积太大了。我们如果只要渲染首页，就应该只加载首页的 js 文件，其他文件不应该加载。所以我们需要将打包生成的文件进行代码分割，生成多个 js 文件，渲染哪个页面就只加载某个 js 文件，这样加载的资源就少，速度就更快；
&nbsp;&nbsp;&nbsp;&nbsp;代码分割（Code Split）主要做了两件事：1、分割文件：将打包生成的文件进行分割，生成多个 js 文件。2、按需加载：需要哪个文件就加载哪个文件；
spa 单页面应用的配置如下：

```javascript
optimization: {
    // 代码分割配置
    splitChunks: {
      chunks: "all", // 对所有模块都进行分割
      // 以下是默认值
      // minSize: 20000, // 分割代码最小的大小
      // minRemainingSize: 0, // 类似于minSize，最后确保提取的文件大小不能为0
      // minChunks: 1, // 至少被引用的次数，满足条件才会代码分割
      // maxAsyncRequests: 30, // 按需加载时并行加载的文件的最大数量
      // maxInitialRequests: 30, // 入口js文件最大并行请求数量
      // enforceSizeThreshold: 50000, // 超过50kb一定会单独打包（此时会忽略minRemainingSize、maxAsyncRequests、maxInitialRequests）
      // cacheGroups: { // 组，哪些模块要打包到一个组
      //   defaultVendors: { // 组名
      //     test: /[\\/]node_modules[\\/]/, // 需要打包到一起的模块
      //     priority: -10, // 权重（越大越高）
      //     reuseExistingChunk: true, // 如果当前 chunk 包含已从主 bundle 中拆分出的模块，则它将被重用，而不是生成新的模块
      //   },
      //   default: { // 其他没有写的配置会使用上面的默认值
      //     minChunks: 2, // 这里的minChunks权重更大
      //     priority: -20,
      //     reuseExistingChunk: true,
      //   },
      // },
  },
```

以上注释的都是默认配置，不需要另外配置；

### 4.2 Preload / Prefetch

&nbsp;&nbsp;&nbsp;&nbsp;我们前面已经做了代码分割，同时会使用 import 动态导入语法来进行代码按需加载（我们也叫懒加载，比如路由懒加载就是这样实现的）。但是加载速度还不够好，比如：是用户点击按钮时才加载这个资源的，如果资源体积很大，那么用户会感觉到明显卡顿效果。我们想在浏览器空闲时间，加载后续需要使用的资源。我们就需要用上 Preload 或 Prefetch 技术。
**两者都能预加载资源有缓存，但是存在兼容性问题**
1、下载插件

```javascript
npm i @vue/preload-webpack-plugin -D
```

2、配置

```javascript
 new PreloadWebpackPlugin({
      rel: "preload", // preload兼容性更好
      as: "script",
      // rel: 'prefetch' // prefetch兼容性更差
    }),
```

### 4.3 Network Cache

将来开发时我们对静态资源会使用缓存来优化，这样浏览器第二次请求资源就能读取缓存了，速度很快。但是这样的话就会有一个问题, 因为前后输出的文件名是一样的，都叫 main.js，一旦将来发布新版本，因为文件名没有变化导致浏览器会直接读取缓存，不会加载新资源，项目也就没法更新了。所以我们从文件名入手，确保更新前后文件名不一样，这样就可以做缓存了。
**配置：**

```javascript
output: {
    path: path.resolve(__dirname, "../dist"), // 生产模式需要输出
    // [contenthash:8]使用contenthash，取8位长度
    filename: "static/js/[name].[contenthash:8].js", // 入口文件打包输出资源命名方式
    chunkFilename: "static/js/[name].[contenthash:8].chunk.js", // 动态导入输出资源命名方式
    assetModuleFilename: "static/media/[name].[hash][ext]", // 图片、字体等资源命名方式（注意用hash）
    clean: true,
  },
```

```javascript
// 提取css成单独文件
    new MiniCssExtractPlugin({
      // 定义输出文件名和目录
      filename: "static/css/[name].[contenthash:8].css",
      chunkFilename: "static/css/[name].[contenthash:8].chunk.css",
    }),
```

**以上配置出现的问题：**
当我们修改 math.js 文件再重新打包的时候，因为 contenthash 原因，math.js 文件 hash 值发生了变化（这是正常的）。但是 main.js 文件的 hash 值也发生了变化，这会导致 main.js 的缓存失效。明明我们只修改 math.js, 为什么 main.js 也会变身变化呢？
**原因分析：**
更新前：math.xxx.js, main.js 引用的 math.xxx.js
更新后：math.yyy.js, main.js 引用的 math.yyy.js, 文件名发生了变化，间接导致 main.js 也发生了变化
**解决办法：**
将 hash 值单独保管在一个 runtime 文件中。我们最终输出三个文件：main、math、runtime。当 math 文件发送变化，变化的是 math 和 runtime 文件，main 不变。runtime 文件只保存文件的 hash 值和它们与文件关系，整个文件体积就比较小，所以变化重新请求的代价也小。
**添加配置如下：**

```javascript
 // 提取runtime文件
    runtimeChunk: {
      name: (entrypoint) => `runtime~${entrypoint.name}`, // runtime文件命名规则
    },
```

### 4.4 Core-js

&nbsp;&nbsp;&nbsp;&nbsp;过去我们使用 babel 对 js 代码进行了兼容性处理，其中使用@babel/preset-env 智能预设来处理兼容性问题。它能将 ES6 的一些语法进行编译转换，比如箭头函数、点点点运算符等。但是如果是 async 函数、promise 对象、数组的一些方法（includes）等，它没办法处理。所以此时我们 js 代码仍然存在兼容性问题，一旦遇到低版本浏览器会直接报错。所以我们想要将 js 兼容性问题彻底解决；
&nbsp;&nbsp;&nbsp;&nbsp;core-js 是专门用来做 ES6 以及以上 API 的 polyfill。polyfill 翻译过来叫做垫片/补丁。就是用社区上提供的一段代码，让我们在不兼容某些新特性的浏览器上，使用该新特性；
1、下载包

```javascript
npm i @babel/eslint-parser -D
```

2、在.eslintrc.js 配置

```javascript
parser: "@babel/eslint-parser", // 支持最新的最终 ECMAScript 标准
```

3、下载 corejs 包

```javascript
npm i core-js
```

4、在 babel.config.js 中配置

```javascript
module.exports = {
  // 智能预设：能够编译ES6语法
  presets: [
    [
      '@babel/preset-env',
      // 按需加载core-js的polyfill
      {useBuiltIns: 'usage', corejs: {version: '3', proposals: true}}
    ]
  ]
}
```

### 4.5 PWA

开发 Web App 项目，项目一旦处于网络离线情况，就没法访问了。我们希望给项目提供离线体验。渐进式网络应用程序(progressive web application - PWA)：是一种可以提供类似于 native app(原生应用程序) 体验的 Web App 的技术。其中最重要的是，在 离线(offline) 时应用程序能够继续运行功能。内部通过 Service Workers 技术实现的。
1、下载包

```javascript
npm i workbox-webpack-plugin -D
```

2、配置插件

```javascript
new WorkboxPlugin.GenerateSW({
      // 这些选项帮助快速启用 ServiceWorkers
      // 不允许遗留任何“旧的” ServiceWorkers
      clientsClaim: true,
      skipWaiting: true,
    }),
```

3、问题
此时如果直接通过 VSCode 访问打包后页面，在浏览器控制台会发现 SW registration failed。因为我们打开的访问路径是：http://127.0.0.1:5500/dist/index.html。此时页面会去请求 service-worker.js 文件，请求路径是：http://127.0.0.1:5500/service-worker.js，这样找不到会 404。实际 service-worker.js 文件路径是：http://127.0.0.1:5500/dist/service-worker.js。
4、解决办法
4.1 下载包

```javascript
npm i serve -g
```

4.2 运行指令

```javascript
serve dist
```

## 总结

我们从 4 个角度对 webpack 和代码进行了优化：
**1、提升开发体验**
使用 Source Map 让开发或上线时代码报错能有更加准确的错误提示。
**2、提升 webpack 提升打包构建速度**
使用 HotModuleReplacement 让开发时只重新编译打包更新变化了的代码，不变的代码使用缓存，从而使更新速度更快。
使用 OneOf 让资源文件一旦被某个 loader 处理了，就不会继续遍历了，打包速度更快。
使用 Include/Exclude 排除或只检测某些文件，处理的文件更少，速度更快。
使用 Cache 对 eslint 和 babel 处理的结果进行缓存，让第二次打包速度更快。
使用 Thead 多进程处理 eslint 和 babel 任务，速度更快。（需要注意的是，进程启动通信都有开销的，要在比较多代码处理时使用才有效果）
**3、减少代码体积**
使用 Tree Shaking 剔除了没有使用的多余代码，让代码体积更小。
使用 @babel/plugin-transform-runtime 插件对 babel 进行处理，让辅助代码从中引入，而不是每个文件都生成辅助代码，从而体积更小。
使用 Image Minimizer 对项目中图片进行压缩，体积更小，请求速度更快。（需要注意的是，如果项目中图片都是在线链接，那么就不需要了。本地项目静态图片才需要进行压缩。）
**4、优化代码运行性能**
使用 Code Split 对代码进行分割成多个 js 文件，从而使单个文件体积更小，并行加载 js 速度更快。并通过 import 动态导入语法进行按需加载，从而达到需要使用时才加载该资源，不用时不加载资源。
使用 Preload / Prefetch 对代码进行提前加载，等未来需要使用时就能直接使用，从而用户体验更好。
使用 Network Cache 能对输出资源文件进行更好的命名，将来好做缓存，从而用户体验更好。
使用 Core-js 对 js 进行兼容性处理，让我们代码能运行在低版本浏览器。
使用 PWA 能让代码离线也能访问，从而提升用户体验。

## 写在最后

> 🥂(❁´◡`❁)您的<font  color=red size=3>点赞</font>👍➕<font  color=red size=3>评论</font>📝➕<font  color=red size=3>收藏</font>⭐ 是作者创作的最大动力 🤞
