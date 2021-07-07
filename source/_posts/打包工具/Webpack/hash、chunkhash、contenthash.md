---
title: hash、chunkhash、contenthash
date: 2021-07-07 11:25:36
categories:
- 打包工具
tags:
- Webpack
comments: true
---

##  [背景](https://juejin.cn/post/6844904007362674701#heading-10)

哈希一般是结合 CDN 缓存来使用的。如果文件内容改变的话，那么对应文件哈希值也会改变，对应的 HTML 引用的 URL 地址也会改变，触发 CDN 服务器从源服务器上拉取对应数据，进而更新本地缓存

<!-- more -->

![img](https://api2.mubu.com/v3/document_image/c491d435-d8e8-419e-a0f0-9e7e84e74ffe-193252.jpg)



##  hash

  - 计算与整个项目的构建相关

  - 代表每次 webpack 编译中生成的 hash 值，所有使用这种方式的文件 hash 都相同

  - 项目有任何变动，hash都会变

  - hash 一变，缓存一定失效了，就没办法实现 CDN 和浏览器缓存



##  chunkhash

  - 计算与同一 chunk 内容相关

  - 基于入口文件（entry）及其关联的 chunk 生成，某个文件的改动只会影响与他有关联的chunk的hash值，不会影响其他文件

  - 同一个chunk内容变了，chunkhash才变



##  contenthash

  - 计算与文件内容本身相关

  - 每个文件都有唯一的hash值

  - 文件发生变化，hash值就会变，不会影响和它同一个模板下的其他文件