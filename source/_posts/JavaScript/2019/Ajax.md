---
title: Ajax
date: 2019-03-07 12:32:37
categories:
- JavaScript
tags:
- Ajax
comments: true
---

## 前言

Ajax 全称(Async Javascript and XML) = 异步 JavaScript + XML

一种创建交互式网页应用的网页开发技术，可以在不重新加载整个网页的情况下，与服务器交换数据，并且更新部分网页

**技术核心**： XMLHttpRequest 对象

**请求过程**：创建XMLHttpRequest、连接服务器、发送异步请求、服务器做出相应、接收数据、用`JavaScript`来操作`DOM`而更新页面<img src="https://mmbiz.qpic.cn/mmbiz_png/gH31uF9VIibTPmdlT5C9O3SHX0bXIug9w5tHOgUaU1GDuOuCRNcGOOzK15pb7rLLBvTiaiaP13qhmugJQGRKnZ1iaA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1" alt="图片" style="zoom: 67%;" />

<!-- more -->

## 原生 Ajax 的工作流程

1. 创建XMLHttp对象

```js
var xmlhttp = window.XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject("Microsoft.XMLHTTP")

标准浏览器：使用XMLHttpRequest对象
IE浏览器：用ActiveXObject对象
```

2. 调用对象的 open方法 与服务器建立连接，其中规定了 请求的类型、URL 以及是否异步处理请求


```js
open(method, url, async)

method：请求的类型 GET/POST
url：文件在服务器上的位置
async：true（异步 默认） / false（同步）
```

3. 如果为异步请求，使用 **onreadystatechange事件** 来监听服务器端的通信状态，当 **readyState 属性为 4** 时，就会调用 执行函数![图片](https://mmbiz.qpic.cn/mmbiz_png/gH31uF9VIibTPmdlT5C9O3SHX0bXIug9wUwPeH1AkgBkVCzibXRkSDNGibBFYr5fr253AQXTlZFLwyCj0BcOricWGw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

```js
readyState
0：请求未初始化
1：服务器连接已建立
2：请求已接收
3：请求处理中
4：请求已完成，且相应已就绪
```

4. 调用 send() 将请求发给服务器

   body: 在 `XHR` 请求中要发送的数据体，如果不传递数据则为 null

   如果使用`GET`请求发送数据的时候，需要注意如下：

   - 将请求数据添加到`open()`方法中的`url`地址中
   - 发送请求数据中的`send()`方法中参数设置为`null`


```js
// 1、创建XMLHttpRequest对象
var xhr = window.XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject("Microsoft.XMLHTTP");

// 2、设置请求参数
xhr.open(请求方式,请求地址,异步或同步);

// 3、设置回调函数
xhr.onreadystatechange = function(){
    if(xhr.reasyState===4){
        if(xhr.status === 200) {
        	//5、接受响应
        	console.log(xhr.responseText); // XMLHttpRequest.responseText属性用于接收服务器端的响应结果
        }
    }
}

// 4、发送请求
xhr.send([body]);
```

5. 接受并处理服务端向客户端响应的数据结果，将处理结果更新到 html 页面中

   

### 自定义header的字段

发送之前用 **setRequestHeader(header, value)** 向请求添加http头



### 封装

```js
//封装一个ajax请求
function ajax(options) {
  //创建XMLHttpRequest对象
  const xhr = new XMLHttpRequest()


  //初始化参数的内容
  options = options || {}
  options.type = (options.type || 'GET').toUpperCase()
  options.dataType = options.dataType || 'json'
  const params = options.data

  //发送请求
  if (options.type === 'GET') {
    xhr.open('GET', options.url + '?' + params, true)
    xhr.send(null)
  } else if (options.type === 'POST') {
    xhr.open('POST', options.url, true)
    xhr.send(params)

    //接收请求
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4) {
        let status = xhr.status
        if (status >= 200 && status < 300) {
          options.success && options.success(xhr.responseText, xhr.responseXML)
        } else {
          options.fail && options.fail(status)
        }
      }
    }
  }

  ajax({
    type: 'post',
    dataType: 'json',
    data: {},
    url: 'https://xxxx',
    success: function(text,xml){//请求成功后的回调函数
      console.log(text)
    },
    fail: function(status){////请求失败后的回调函数
      console.log(status)
    }
  })
```



## 跨域携带cookie

跨域请求时，由于浏览器安全 策略，不会自动携带cookie到服务器

### 原生 Ajax 开启

我们可以设置 XMLHttpRequest.withCredentials ， xmlhttp.setRequestHeader('Cookie', document.cookie) 是不安全的做法

```js
XMLHttpRequest.withCredentials=true;
xmlhttp.setRequestHeader('Cookie', document.cookie) // error
```

### Jq -> Ajax开启

```js
 $.ajax({
    type: "POST",
    url: "http://xxx.com/api/test",
    dataType: 'jsonp',
    xhrFields: {withCredentials: true},
    crossDomain: true,
})

服务器端设置：
header("Access-Control-Allow-Credentials: true");
header("Access-Control-Allow-Origin: http://www.xxx.com")
```



## 用途 / 好处

- 实现客户端服务器的异步通信效果，带来更好的用户体验
- 实现页面的局部刷新，按需获取数据，节约带宽资源



## 缺点

- 不支持浏览器back按钮
- 安全问题，暴露了与服务器交互的细节
- 对搜索引擎的支持比较弱：如果使用不当，会增大网络数据的流量，从而降低整个系统的性能