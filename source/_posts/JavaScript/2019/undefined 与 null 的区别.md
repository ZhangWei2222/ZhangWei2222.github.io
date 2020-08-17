---
title: undefined 与 null 的区别
date: 2019-04-09 15:32:37
categories:
- JavaScript
tags:
comments: true
---

### 数据类型的区别

`undefined` 和 `null` 是两个基本类型

<!-- more -->



### 存在场景不同

- `undefined` 是未初始化变量的值

- `null` 通常用来标识一个空对象

```js
typeof(undefined) ==undefined
typeof(null) ==object
```



### 判断undefined方法

```js
typeof()
```



### 判断null方法

```js
typeof(x) !='undefined' && !x && x!=0
// x为false有3种情况：0 undefined null 空字符串，''等于0
```



### 其他

```js
10 + null = 10
10 + undefined = NaN

undefined == true //false
undefined == false //false

null == true //false
null == false //false
```

