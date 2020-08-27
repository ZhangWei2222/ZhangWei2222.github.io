---
title: Vue.js 运行机制全局概览
date: 2020-07-05 17:00:47
categories:
- Vue
comments: true
---

## 全局概览

<img src="C:\Users\Administrator\Desktop\ZhangWei2222.github.io\source\_posts\Vue\剖析 Vue.js 内部运行机制\Vue.js 运行机制全局概览.assets\image-20200705205036620.png" alt="image-20200705205036620" style="zoom:50%;" />

接下来依次介绍模块的作用以及调用关系

<!-- more -->



## 初始化及挂载

<img src="C:\Users\Administrator\Desktop\ZhangWei2222.github.io\source\_posts\Vue\剖析 Vue.js 内部运行机制\Vue.js 运行机制全局概览.assets\1606e8abbababbe6.png" alt="img" style="zoom: 50%;" />

在 `new Vue()` 之后，Vue 会调用 `_init` 函数进行初始化，也就是这里的 `init` 过程，它会初始化生命周期、事件、props、methods、data、computed 与 watch 等。其中最重要的是通过`Object.defineProperty` 设置 `setter` 和 `getter` 函数，用来实现【**响应式**】以及【**依赖收集**】。

初始化之后调用 `$mount` 会挂载组件。如果是运行时编译，即不存在 render function 但是存在 template 的情况，需要进行【**编译**】步骤。



## 编译

<img src="C:\Users\Administrator\Desktop\ZhangWei2222.github.io\source\_posts\Vue\剖析 Vue.js 内部运行机制\Vue.js 运行机制全局概览.assets\1606ec3d306ab28f.png" alt="img" style="zoom:50%;" />

complie 编译可以分成 `parse`、`optimize` 与 `generate` 三个阶段，最终得到渲染 VNode 所需的 render function。

### parse

用正则等方式解析 template 模板中的指令、class、style 等数据，形成 AST 抽象语法树。

### optimize

主要作用是标记 static 静态节点。这是 Vue 在编译过程中的一处优化，后面 `update` 更新界面时，会有一个 `patch` 的过程，diff 算法会直接跳过静态节点，从而减少了比较的过程，优化了 `patch` 的性能。

### generate

将 AST 抽象语法树转化成 render function 字符串的过程，得到的结果是 **render 的字符串以及 staticRenderFns 字符串**。 



## 响应式

<img src="C:\Users\Administrator\Desktop\ZhangWei2222.github.io\source\_posts\Vue\剖析 Vue.js 内部运行机制\Vue.js 运行机制全局概览.assets\1606edad5ca9e23d.png" alt="img" style="zoom: 50%;" />

当 render function 被渲染的时候，因为会读取所需对象的值，所以会触发 `getter` 函数进行【**依赖收集**】，【**依赖收集**】的目的是将观察者 Watcher 对象存放到当前闭包中的订阅者 Dep 的 subs 中。 

<img src="C:\Users\Administrator\Desktop\ZhangWei2222.github.io\source\_posts\Vue\剖析 Vue.js 内部运行机制\Vue.js 运行机制全局概览.assets\160770b2a77e084e.png" alt="img" style="zoom: 80%;" />

在修改对象的值的时候，会触发对应的 `setter`，`setter` 通知之前【**依赖收集**】得到的 Dep 中的每一个 Watcher，告诉它们自己的值改变了，需要重新渲染视图。这时候这些 Watcher 就会开始调用 `update` 来更新视图。



## Virtual DOM

render funciton 会被转化成 VNode 节点。Virtual DOM 其实就是一棵以 JavaScript 对象（VNode 节点）作为基础的树，用对象属性来描述节点。

实际上它只是一层对真实 DOM 的抽象，最终可以通过一系列操作使这棵树隐射到真实环境上。由于 Virtual DOM 是以 JavaScript 对象为基础而不依赖真实平台环境，所以它具有跨平台的能力，比如：浏览器平台、Weex、Node等。

```vue
{
    tag: 'div',                 /*说明这是一个div标签*/
    children: [                 /*存放该标签的子节点*/
        {
            tag: 'a',           /*说明这是一个a标签*/
            text: 'click me'    /*标签的内容*/
        }
    ]
}

// 渲染后
<div>
    <a>click me</a>
</div>

//这只是一个简单的例子，实际上的节点有更多的属性来标志节点，比如 isStatic （代表是否为静态节点）、 isComment （代表是否为注释节点）等。
```



## 更新视图

在修改一个对象值的时候，会通过 `setter -> Wtacher -> update` 的流程来修改对应的视图，那么最终是如何更新视图的呢？

当数据变化后，执行 render function 就可以得到一个新的 VNode 节点，然后会将新的 VNode 与旧的 VNode 一起传入 `patch` 进行比较，经过 diff 算法得到它们的【**差异**】，最后只需要将这些【**差异**】的对应 DOM 进行修改即可。