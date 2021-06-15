---
title: WebSocket
date: 2021-06-15 14:28:37
categories:
- 计算机网络
comments: true
---

WebSocket是HTML5开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。在WebSocket API中，浏览器和服务器只需要做一个握手的动作，然后，浏览器和服务器之间就形成了一条快速通道。两者之间就直接可以数据互相传送。



浏览器通过 JavaScript 向服务器发出建立 WebSocket 连接的请求，连接建立以后，客户端和服务器端就可以通过 TCP 连接直接交换数据。当你获取 Web Socket 连接后，你可以通过 send() 方法来向服务器发送数据，并通过 onmessage 事件来接收服务器返回的数据。

![img](http://s11.mogucdn.com/mlcdn/c45406/190614_1kc3gejefi019kc01lg3fhl834bgf_748x348.png)



参考文章：[说说对WebSocket的理解？应用场景？](https://mp.weixin.qq.com/s?__biz=MzU1OTgxNDQ1Nw==&mid=2247487708&idx=2&sn=9576b6d5bf54abe6a5b2fe2ad755ad9a&chksm=fc10d28acb675b9cbe33def9e7b2ad5a681f9b19c83efd484a3f65081a71b31b35761328122e&scene=178&cur_album_id=1867268148068941825#rd)

