---
title: Object.keys
date: 2019-11-01 18:24:37
categories:
- JavaScript
comments: true
---

## 介绍

处理对象，返回可枚举的属性数组

```js
let person = {
  name:"张三",
  age:25,
  address:"深圳",
  getName:function(){}
}
Object.keys(person) // ["name", "age", "address","getName"]
```

<!-- more --> 

## 应用

用于：获取对象的长度

```js
let person = {
  name:"张三",
  age:25,
  address:"深圳",
  getName:function(){}
}
let arr = Object.keys(person); // ["name", "age", "address","getName"]
console.log(arr.length); // 4
```

