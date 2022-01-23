---
title: Js 精度问题
date: 2019-11-06 18:13:37
categories:
- JavaScript
comments: true
---



## 前言

Js 浮点数运算会出现意想不到的结果：` 0.1+0.2=0.30000000000000000004`

<!-- more -->



## 为什么

计算机无法直接计算十进制的数字，会先按照 IEEE754 算术标准转成相应的二进制，然后对阶运算，最后运算。二进制转换和对阶运算过程会出现精度损失



## 怎么解决

1. 将数字转换成整数运算，之后再转换成对应的小数

   ```js
   (0.1 * 10 + 0.2 * 10) 
   ```

2. 使用 toFixed() 方法 

   toFixed()：将数字转换成字符串，小数为传入的数字

   ```js
   parseFloat((0.1 + 0.2).toFixed(10)) === 0.3
   ```

   



```js
/**
 * 小数加、减、乘、除 保留精度计算
 * @param {Number} value
 * @param {Number} decimals
 * for example
 * precisionRound(0.1 + 0.2, 2) 返回结果0.3
 */
export const precisionRound = (value, decimals) => {
  return Number(`${Math.round(`${value}e${decimals}`)}e-${decimals}`)
}
// 用公式
precisionRound(0.1+0.2, 2)
```



## 参考文章

[0.1 + 0.2不等于0.3？为什么JavaScript有这种“骚”操作？](https://juejin.cn/post/6844903680362151950)

