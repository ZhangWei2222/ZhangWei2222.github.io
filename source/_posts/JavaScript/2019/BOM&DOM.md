---
title: BOM&DOM
date: 2019-04-17 15:32:37
categories:
- JavaScript
comments: true
---

## BOM & DOM
### [BOM 浏览器对象模型](https://mp.weixin.qq.com/s?__biz=MzU1OTgxNDQ1Nw==&mid=2247486108&idx=2&sn=d8feb34e4273ccca56e79853030e7036&chksm=fc10c8cacb6741dce70f20ce120ee69fef2a8310513a62af0b84dc9f838f8f16a0d4cd0ae7ce&scene=178&cur_album_id=1722697362059362305#rd)

提供了独立于内容与浏览器窗口进行交互的对象

其作用就是跟浏览器做一些交互效果,比如如何进行页面的后退，前进，刷新，浏览器的窗口发生变化，滚动条的滚动，以及获取客户的一些信息如：浏览器品牌版本，屏幕分辨率

浏览器的全部内容可以看成`DOM`，整个浏览器可以看成`BOM`。区别如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/gH31uF9VIibSB8n80TmW7TYzgj9SpW1MSVONgTEkBn8xEq3DHRWH7Ycv5pU2CC3sUt7jXVX4eaFviabXCcWDYozg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



有window、location、history、screen等对象



### DOM 文档对象模型 

文档对象模型 (DOM) 是 `HTML` 和 `XML` 文档的编程接口

它提供了对文档的结构化的表述，并定义了一种方式可以使从程序中对该结构进行访问，从而改变文档的结构，样式和内容

任何 `HTML`或`XML`文档都可以用 `DOM`表示为一个由节点构成的层级结构

#### [**操作**]()

- 创建节点

- 查询节点

- 更新节点

- 添加节点

- 删除节点

  

## Global对象 &  window对象

### Global对象

单体内置对象，即不依赖于宿主环境的对象，这些对象在ECMAScript程序执行之前就已经存在了，就是一切全局里存在的变量和函数都是它的属性和方法。也就是那些在宿主环境（常见的宿主环境：浏览器和nodejs）里所有的内建或自定义的变量和函数全局都是Global这个的全局对象和方法。它更像是一个抽象概念，而要指明它是什么，取决于程序在什么环境中运行。（例如：js不仅可以书写页面，在书写页面中，global相对于浏览器这个环境而言就是window，但是其他环境就不一定了）

### window对象

是相对于Web浏览器而言的，它并不是ECMAScript规定的内置对象，它是浏览器的Web API,是存在于浏览器之中的，也就是离开浏览器这个宿主环境的话就不存在此对象了。

《JavaScript高级程序设计第3版》：在所有代码执行之前，作用域中就存在两个内置对象：Global和Math。在大多数ECMAScript实现中都不能直接访问Global对象，不过，Web浏览器实现了承担该角色的window对象。全局变量和函数都是Globla对象的属性。

#### 常用的方法：

open()   打开一个新的资源。

moveTo() 将窗口左上角的屏幕位置移动到指定的 x 和 y 位置。

moveBy()  相对于当前的窗口移动指定的 x 和 y 偏移值(左上角)。

setInterval()  每隔指定的毫秒数指定指定的代码。

setTimeout（） 经过指定毫秒数指定一次指定的代码。 



### 结论

JavaScript中的window对象扮演ECMAScript中的Global对象，所以Global对象包含着window对象