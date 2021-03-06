---
title:  URL编码
date: 2019-09-02 18:11:37
categories:
- 浏览器
comments: true
---

## 前言

HTTP协议中，参数的传输按照键值对的形式：’key = value‘

/ ? % # & =是 URL 的保留字符，/是地址分隔符，？后面接参数串，#表示书签，&参数间分隔符，=指定参数值

当参数内有/ ? % # & = ，是需要进行转义的，为了不与保留字符产生冲突。下表为特殊字符的转义表

<!-- more -->

## 转义表

|      |                                |      |
| ---- | ------------------------------ | ---- |
| +    | URL中的 +号 表空格             | %2B  |
| 空格 | URL中的空格可以用：+号或者编码 | %20  |
| /    | 分割目录、子目录               | %2F  |
| ？   | 分割地址与参数                 | %3F  |
| %    | 特殊字符                       | %25  |
| #    | 书签                           | %23  |
| &    | 参数见的分隔符                 | %26  |
| =    | 指定参数的值                   | %3D  |