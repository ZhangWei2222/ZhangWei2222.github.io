---
title: Rollup 初探
date: 2020-07-20 10:26:36
categories:
- 打包工具
comments: true
---



## 前言

最近项目中使用了一个打包工具：Rollup 而非 Webpack，很惊讶为何在 Webpack 横行的今天，Rollup 也能有自己的一席之位，其优势和特点是什么呢？初探，go!

<!-- more -->



## 是什么

Rollup 是 javascript 打包器

- 背景：前端模块化方案混杂，es6 模块化横空出世
- 目的：希望 es6 模块化统治天下、推进模块化方式统一
- 做法：
  - 帮助开发者使用 es6，在开发者使用 es6 模块化后，打包生成各个环境的规范标准（common.js、amd、iife、umd、systemjs、es）
  - 在开发者使用 es6 后，帮助其优化精简代码，使只保留代码中需要的部分
  - 在插件拓展中支持对其他模块化规范、js 语言类型、css 及 css 框架和媒体资源的打包
  - 打包基本功能（热更新、排除包、sourceMap...）



## 为什么

<img src="https://img.youpin.mi-img.com/jianyu/9c341426240f1c8b45e60c7b73aac4e0.png?w=1024&amp;h=301" alt="https://img.youpin.mi-img.com/jianyu/9c341426240f1c8b45e60c7b73aac4e0.png?w=1024&amp;h=301" style="zoom:67%;" />

<img src="https://img.youpin.mi-img.com/jianyu/e7f5301436d822e141117ce9d297f9a6.png?w=1024&amp;h=509" alt="https://img.youpin.mi-img.com/jianyu/e7f5301436d822e141117ce9d297f9a6.png?w=1024&amp;h=509" style="zoom:67%;" />

### Tree-shaking

- 目的：将没有用的代码摇出来
- Rollup 较早提出的 DCE（dead code elimination）的一种延伸
- 永远也不会执行的代码
- 执行了也没有用的代码
- 实现了跨文件 DCE



### es6 模块化特点（Import、export）

- 文件头部通过 import 引用需要模块
- 引用模块名是字符串常量 import axios from 'axios'
- import 进来的变量是不可以改变的 axios =*** （不可）
- 可以支持 {item} 按需引用
- 可以进行静态语言分析



###Tree-shaking 对比实验

<img src="https://img.youpin.mi-img.com/jianyu/2f284221c0abd8dab72a556631b2d28a.png?w=972&amp;h=669" alt="https://img.youpin.mi-img.com/jianyu/2f284221c0abd8dab72a556631b2d28a.png?w=972&amp;h=669" style="zoom:67%;" />

**webpack 5 alpha**

<img src="https://img.youpin.mi-img.com/jianyu/d516d3cf729437ffbf803bacb115fcc7.png?w=982&amp;h=620" alt="https://img.youpin.mi-img.com/jianyu/d516d3cf729437ffbf803bacb115fcc7.png?w=982&amp;h=620" style="zoom:67%;" />



### 官方吐槽（中英文网对比）

**Rollup 是用来构建库还是应用程序？(Is Rollup meant for building libraries or applications?)**

Rollup 已被许多主流的 JavaScript 库使用，也可用于构建绝大多数应用程序。但是 Rollup 还不支持一些特定的高级功能，尤其是用在构建一些应用程序的时候，特别是代码拆分和运行时态的动态导入 [dynamic imports at runtime](https://github.com/tc39/proposal-dynamic-import). 如果你的项目中更需要这些功能，那使用 [Webpack](https://webpack.js.org/)可能更符合你的需求。



**Is Rollup meant for building libraries or applications**

Rollup is already used by many major JavaScript libraries, and can also be used to  build the vast majority of applications. However if you want to use code-splitting or dyncmic imports with older browsers, you will need an additional runtime to handle loading missing chunks. We recommend using the SystiemJs Production Build as it integrates nicely with Rollup's system format output and is capable of properly handling all the ES module live bindings and re-export edge cases.Alternatively, an AMD loader can be used as well.



由此可见，中文网更新滞后。。



## 怎么用

全局安装rollup  `npm install rollup -g`

- 命令行 `rollup src/a.js -f cjs -o dist/rollup.js`

- API rollup()、watch()

- 配置文件 `rollup.config.js`

  ```js
  import entry from './build/config.json'
  
  export default {
    input: entry.entry,
    output: {
      file: './dist/rollup.js',
      format: 'cjs'
    }
  }
  // 启动
  rollup --config ** -w
  rollup -c -w
  ```



## 流程图

### 构建阶段

<img src="https://img.youpin.mi-img.com/jianyu/4addeecad87a00ad3703fb63f005a582.png?w=1000&amp;h=1277" alt="https://img.youpin.mi-img.com/jianyu/4addeecad87a00ad3703fb63f005a582.png?w=1000&amp;h=1277" style="zoom: 33%;" />

### 处理输出阶段

<img src="https://img.youpin.mi-img.com/jianyu/fbd9085555b261d110e7e90d69096690.png?w=1014&amp;h=1846" alt="https://img.youpin.mi-img.com/jianyu/fbd9085555b261d110e7e90d69096690.png?w=1014&amp;h=1846" style="zoom:33%;" />



## 总结

- 推崇Es6 模块化写法
- 打包结果简洁
- 配置相对简单
- 动态加载需兼容，更适合于类库的打包