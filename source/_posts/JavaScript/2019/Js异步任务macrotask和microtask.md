---
title: Js异步任务macrotask和microtask
date: 2019-07-25 14:42:37
categories:
- JavaScript
tags:
- 运行机制
comments: true
---

## 前言
在阅读 Vue-nextTick源码时，看到有通过MutationObserver去实现nextTick（使用textnode模拟DOM变化，MO监听到后触发回调函数），百思不得其解：为什么可以使用MO这个API，为什么MO要优选与setTimeout？

<!-- more -->

## Js运行机制（刷新三观）

众所周知，JS是单线程的

- 任务进入执行栈，判断是同步任务还是异步任务

- 主线程依次执行同步代码

- 当异步任务有结果时，把事件放入任务队列

- 当主线程的同步代码执行完毕了，又会依次地从任务队列取事件，执行异步任务的回调

<img src="https://mmbiz.qpic.cn/mmbiz_png/gH31uF9VIibSNwS2EBDtHnxMysjAib2yvIiasUT3CvmQkgiaF81VfzQicNuatqbUIcT01iccddryMSgGmTNr8OLUFmvg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1" alt="图片" style="zoom:80%;" />

 **但是重点来了**

任务队列分成tasks 和 microtasks，在每次事件循环中，tasks只会取一个执行，执行完成会检查microtasks队列，并把里面所有的任务都执行完毕。

<img src="https://mmbiz.qpic.cn/mmbiz_png/gH31uF9VIibSNwS2EBDtHnxMysjAib2yvIh75NUHZic4J4n5mPSe5jcMcWtsO0LuhRDuqdHVWicqejmPAI8NLLjuKw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1" alt="图片" style="zoom:67%;" />

上述过程不断重复就是 event loop（事件循环）



## API

### macrotasks(tasks)

宏任务的时间粒度比较大，执行的时间间隔是不能精确控制的，对一些高实时性的需求就不太符合

常见的宏任务有：

- script (可以理解为外层同步代码)

- setTimeout/setInterval

- UI rendering/UI事件

- postMessage、MessageChannel

- setImmediate、I/O（Node.js）

- requestAnimationFrame

  

### microtasks

一个需要异步执行的函数，执行时机是在主函数执行结束之后、当前宏任务结束之前

常见的微任务有：

- process.nextTick（Node.js）
- Promise.then
- MutationObserver
- Object.observe（已废弃；Proxy 对象替代）

```javascript
console.log(1);
setTimeout(function(){
  console.log(2);
}, 0);
Promise.resolve().then(function(){
  console.log(3);
}).then(function(){
  console.log(4);
});
结果：1 3 4 2 (promise比setTimeout优先级高)
```



## 注意

根据HTML Standrad，在每个task运行完以后，UI都会重新渲染，那么在microtask完成数据更新，当前task结束后就能得到新的UI；反之新建一个task来做数据更新，UI就会渲染两次



## 总结

现在我们就可以回答前言提到的两个问题啦：
- 为什么可以使用MO这个API
	- 它属于一个异步任务，到数据发生变化(Js主线程执行了赋值操作)，就会调用起microtasks任务队列的任务MO(MO做了啥请移动到 Vue-nextTick)
- 为什么MO要优选与setTimeout
	- 因为MO是一个microtask，setTimeout是一个task，task做数据更新，UI会渲染两次，耗费性能



## 补充

```js
// CVTE笔试题2019.09.05
process.nextTick(function(){
    console.log(1);
    process.nextTick(function(){
        console.log(2);
    })
})
setTimeout(function(){
  console.log(4);
}, 0);
process.nextTick(function(){
    console.log(3);
})
console.log(5);
// 5 1 2 3 4
//如果有多个process.nextTick语句（不管它们是否嵌套），将全部在当前"执行栈"执行
```

![image-20210621153407573](https://img.youpin.mi-img.com/luban/ov77d438tag_22026080841624261854957.png)![image-20210621153407573](https://img.youpin.mi-img.com/luban/ea5tb0rt2f_22026080841624261854900.png)

