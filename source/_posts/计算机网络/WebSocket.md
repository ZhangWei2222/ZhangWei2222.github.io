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





## 是什么

一个网络协议，可实现客户端与服务端间的全双工通信，即支持服务端自主推送，同时还支持跨域

基于 HTTP协议完成握手 



## 有什么作用

主要是为了提高效率，减少带宽

> 在 websocket 出现之前，开发实时 web 应用的方式为轮询
>
> 不停地向服务器发送 HTTP 请求，问有没有数据，有数据的话服务器就用响应报文回应。如果轮询的频率比较高，那么就可以近似地实现“实时通信”的效果 轮询的缺点也很明显，反复发送无效查询请求耗费了大量的带宽和 CPU资源



## 如何使用

通过 onopen、onmessage、onclose、onerror 四个事件的实现来处理 websocket 的响应

连接 websocket 后，可以通过 send() 方法来向服务器发送数据



## 与HTTP、TCP的关系

WebSocket和HTTP都属于应用层协议，且都是基于TCP的，它们的send函数最终也是通过TCP系统接口来做数据传输。

WebSocket在建立握手连接时，数据是通过HTTP协议传输的，但是在连接建立后，真正的数据传输阶段则不需要HTTP协议的参与。

<img src="https://images2017.cnblogs.com/blog/1247371/201711/1247371-20171120120707258-353166864.gif" alt="img" style="zoom:60%;" />



## 握手

websocket协议通信分为两个部分，先是握手，再是数据传输。

### 客户端发送数据格式

```
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Origin: http://example.com
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
```

- Connection：必须设置Upgrade，表示客户端希望连接升级

- Upgrade：必须设置Websocket，表示希望升级到Websocket协议

- Sec-WebSocket-Key：客户端发送的一个 base64 编码的密文，用于简单的认证秘钥。要求服务端必须返回一个对应加密的“Sec-WebSocket-Accept应答，否则客户端会抛出错误，并关闭连接

- Sec-WebSocket-Version ：表示支持的Websocket版本

### 服务端返回的数据格式

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
Sec-WebSocket-Protocol: chat
```

- HTTP/1.1 101 Switching Protocols：表示服务端接受 WebSocket 协议的客户端连接

- Sec-WebSocket-Accep：验证客户端请求报文，同样也是为了防止误连接。具体做法是把请求头里“Sec-WebSocket-Key”的值，加上一个专用的 UUID，再计算摘要



## 应用场景

基于websocket的实时通信的特点

- 弹幕
- 媒体聊天
- 协同编辑
- 基于位置的应用
- 体育实况更新
- 股票基金报价实时更新



## [react 实现聊天室](https://github.com/ZhangWei2222/websocket-chatroom)

### 前端

```react
import { FC, useRef, useState, useEffect } from "react";
import { Button, Input, message } from "antd";
import { UserOutlined } from "@ant-design/icons";
import "./App.scss";

const App: FC = () => {
  let ws: any = useRef(null);

  const [name, setName] = useState("");
  const [hasUser, setHasUser] = useState(false);
  const [content, setContent] = useState("");
  const [chatList, setChatList] = useState<any>([]);

  const chatListRef = useRef<any[]>([]);

  useEffect(() => {
    chatListRef.current = chatList;
    setChatList(chatList);
  }, [chatList]);

  // 名字改变时
  const handleChangeName = (event: any) => {
    setName(event.target.value);
  };

  // 加入聊天室
  const handleAddChat = () => {
    if (!name) {
      message.error("输入不可为空");
      return;
    }

    //将加入房间按钮设置不可用（只能加入一次）
    setHasUser(true);
    ws.current = new WebSocket("ws://localhost:4000");
    //在建立连接时会触发
    ws.current.onopen = function () {
      //向服务器发送消息
      ws.current.send(
        JSON.stringify({
          text: name,
          type: "setName",
        })
      );
    };

    //自动接收服务器返回的数据
    ws.current.onmessage = function (event: any) {
      const data: any = JSON.parse(event.data);
      setChatList([...chatListRef.current, data]);
      chatListRef.current = [...chatListRef.current, data];
    };
  };

  // 聊天内容改变时
  const handleChangeContent = (event: any) => {
    setContent(event.target.value);
  };

  // 发送聊天内容
  const handleSendContent = () => {
    if (!content) {
      message.warn("输入为空哦");
      return;
    }

    ws.current.send(
      JSON.stringify({
        text: content,
        type: "chat",
      })
    );
    setContent("");
  };

  return (
    <div className="App">
      <div className="top">
        <Input
          value={name}
          placeholder="输入用户名"
          prefix={<UserOutlined />}
          disabled={hasUser}
          onChange={handleChangeName}
          onPressEnter={handleAddChat}
          />
        <Button type="primary" onClick={handleAddChat} disabled={hasUser}>
          加入聊天
        </Button>
      </div>

      <div className="center">
        {chatList.map((item: any, index: number) => {
          return (
            <div className="chat-container" key={index}>
              <div className="time">{item.name} 2021-20-12 12:00:00</div>
              <div className="content">{item.text}</div>
            </div>
          );
        })}
      </div>

      <div className="footer">
        <Input
          value={content}
          placeholder="输入内容"
          onChange={handleChangeContent}
          onPressEnter={handleSendContent}
          disabled={!hasUser}
          />
        <Button type="primary" onClick={handleSendContent} disabled={!hasUser}>
          发送
        </Button>
      </div>
    </div>
  );
};

export default App;
```

### 后端

```js
// let express = require('express')
// let app = express()
let WebSocket = require('ws').Server

let wss = new WebSocket({ port: 4000 })
wss.on('connection', (ws) => {
  console.log('服务端-客户端 连接成功');

  //用来处理接收客户端的消息
  ws.on('message', (buffer) => {
    const data = JSON.parse(buffer.toString())
    switch (data.type) {
      case 'setName':
        ws.nickname = data.text;
        broadcast(JSON.stringify({
          name: 'Server',
          text: data.text + '加入了房间'
        }));
        break;
      case 'chat':
        broadcast(JSON.stringify({
          name: ws.nickname,
          text: data.text
        }));
        break;
      default:
        break;
    }
  })

  //当用户关闭网页或者手动关闭网页连接时自动触发
  ws.on('close', function () {
    broadcast(JSON.stringify({
      name: 'Server',
      text: ws.nickname + '离开了房间'
    }));
  });

  //error事件这个必须写，否则当客户端关闭时，后端服务器会崩溃
  ws.on('error', function (err) {
    console.log('关闭客户端', err);
  })

  //循环将消息广播更新给所有人
  function broadcast(value) {
    console.log('进入广播...', value);
    // wss.clients就是可以拿到所有人数组
    // 遍历每一个人发消息
    wss.clients.forEach(function (ws) {
      ws.send(value);//发送消息给客户端
    })
  }
})
```



## 心脏与重连机制

### 目的

客户端和服务端保证彼此还活着，避免丢包发生



### 连接断开的两种情况

#### 前端断开

比如信号不好，或者网络临时关闭，这时候websocket的连接已经断开，而不同浏览器有不同的机制，触发onclose的时机也不同，并不会理想执行websocket的onclose方法，我们无法知道是否断开连接，也就无法进行重连操作。



#### 后端断开

如果后端因为一些情况需要断开ws，在可控情况下，会下发一个断连的消息通知，之后才会断开，我们便会重连。如果因为一些异常断开了连接，我们是不会感应到的。



### 心跳机制

通过在指定时间间隔发送心跳包来保证连接正常，如果连接出现问题，就需要手动触发onclose事件，这时候便可进行重连操作。

<img src="https://segmentfault.com/img/bVcNBBr" alt="未命名文件(1).png" style="zoom:67%;" />



### 简单实现



```html
<html>

  <head>
    <meta charset="utf-8">
    <title>WebSocket Demo</title>
  </head>

  <body>
    <script type="text/javascript">
      var wsUrl = "ws://localhost:9000";
      var ws;
      function createWebSocket() {
        try {
          ws = new WebSocket(wsUrl);
          init();
        } catch (e) {
          console.log('catch');
          reconnect(wsUrl);
        }
      }

      function init() {
        ws.onclose = function () {
          console.log('链接关闭');
          reconnect(wsUrl);
        };
        ws.onerror = function () {
          console.log('发生异常了');
          reconnect(wsUrl);
        };
        ws.onopen = function () {
          //心跳检测重置
          heartCheck.start();
        };
        ws.onmessage = function (event) {
          console.log('接收到消息');
          //拿到任何消息都说明当前连接是正常的
          heartCheck.start();
        }
      }

      var lockReconnect = false;//避免重复连接
      var tt;
      let reconnectNum = 3 // 重连的次数
      function reconnect(url) {
        if (lockReconnect || reconnectNum <= 1) {
          return;
        };

        lockReconnect = true;
        //没连接上会一直重连，设置延迟避免请求过多
        tt && clearTimeout(tt);
        tt = setTimeout(function () {
          createWebSocket(url);
          lockReconnect = false;
          --reconnectNum
        }, 3000);
      }

      // 心跳检测：3次心跳均未响应重连
      var heartCheck = {
        timeout: 3000, // 每隔三秒发送心跳
        num: 3, // 3次心跳均未响应重连
        timeoutObj: null,
        start: function () {
          var _this = this;
          var _num = this.num;
          this.timeoutObj && clearTimeout(this.timeoutObj);
          this.timeoutObj = setTimeout(function () {
            // 这里发送一个心跳，后端收到后，返回一个心跳消息，
            // onmessage拿到返回的心跳就说明连接正常
            ws.send("123456789"); // 心跳包
            _num--;
            //计算答复的超时次数
            if (_num === 0) {
              ws.close();
            }
          }, this.timeout)
        }
      }
      createWebSocket(wsUrl);

      // //心跳检测： 服务端超时时间未响应重连
      // var heartCheck = {
      //   timeout: 3000, //每隔三秒发送心跳
      //   severTimeout: 5000, //服务端超时时间
      //   timeoutObj: null,
      //   serverTimeoutObj: null,
      //   start: function () {
      //     var _this = this;
      //     this.timeoutObj && clearTimeout(this.timeoutObj);
      //     this.serverTimeoutObj && clearTimeout(this.serverTimeoutObj);
      //     this.timeoutObj = setTimeout(function () {
      //       //这里发送一个心跳，后端收到后，返回一个心跳消息，
      //       //onmessage拿到返回的心跳就说明连接正常
      //       ws.send("123456789"); // 心跳包
      //       //计算答复的超时时间
      //       _this.serverTimeoutObj = setTimeout(function () {
      //         ws.close();
      //       }, _this.severTimeout);
      //     }, this.timeout)
      //   }
      // }
    </script>
  </body>

</html>
```

```js
let WebSocket = require('ws').Server

let wss = new WebSocket({ port: 9000 })
wss.on('connection', (ws) => {
    console.log('服务端-客户端 连接成功');

    //用来处理接收客户端的消息
    ws.on('message', (buffer) => {
        ws.send('我是服务端，已收到信息');//发送消息给客户端
    })

    //当用户关闭网页或者手动关闭网页连接时自动触发
    ws.on('close', function () {
        console.log('服务端关闭了');
    });

    //error事件这个必须写，否则当客户端关闭时，后端服务器会崩溃
    ws.on('error', function (err) {
        console.log('关闭客户端', err);
    })
})
```



## Socket.io

### 是什么 / 与 websocket 的关系

WebSocket是HTML5最新提出的规范，虽然主流浏览器都已经支持，但仍然可能有不兼容的情况。

为了兼容所有浏览器，给程序员提供一致的编程体验，**SocketIO 将 WebSocket、AJAX 和其它的通信方式全部封装成了统一的通信接口**。

也就是说，我们在使用SocketIO时，**不用担心兼容问题**，底层会自动选用最佳的通信方式。

因此说，**WebSocket是SocketIO的一个子集**。

> 也就是说，Websocket仅仅是 Socket.io实现实时通信的一个子集。因此Websocket客户端连接不上Socket.io服务端，当然Socket.io客户端也连接不上Websocket服务端。



### 简单例子

```js
<script src="/socket.io/socket.io.js"></script>

<script>
  var socket = io();

var messages = document.getElementById('messages');
var form = document.getElementById('form');
var input = document.getElementById('input');

form.addEventListener('submit', function (e) {
  e.preventDefault();
  if (input.value) {
    // 客户端发送消息
    socket.emit('chat message', input.value);
    input.value = '';
  }
});

// 监听服务端发来的消息
socket.on('chat message', function (msg) {
  console.log('客户端拿到服务端的信息', msg)
  var item = document.createElement('li');
  item.textContent = msg;
  messages.appendChild(item);
  window.scrollTo(0, document.body.scrollHeight);
});
</script>
```

```js
const app = require('express')();
const http = require('http').Server(app);
const io = require('socket.io')(http);
const port = process.env.PORT || 3100;

app.get('/', (req, res) => {
  res.sendFile(__dirname + '/index.html');
});

io.on('connection', (socket) => {
  socket.on('chat message', msg => {
    console.log('服务器拿到客户端的信息', msg)
    io.emit('chat message', '说：' + msg); // 把所有事件发送给每个用户
    // 也可以使用 broadcast 将消息发给除特定 socket 外的其他用户
  });
  socket.on('disconnect', () => {
    console.log('disconnect')
  })
});

http.listen(port, () => {
  console.log(`Socket.IO server running at http://localhost:${port}/`);
});
```



## 参考文章

[Socket.io](https://socket.io/)

[socket.io官方文档中文版](https://zhuanlan.zhihu.com/p/29148869)
