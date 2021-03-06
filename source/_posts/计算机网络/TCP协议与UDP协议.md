---
title:  TCP协议 与 UDP协议
date: 2019-08-29 17:12:37
categories:
- 计算机网络
tags:
- TCP
- UDP
comments: true
---

## 前言

**TCP、UDP**是OSI模型中**运输层**中的协议。我们经常使用 **ping** 命令来测试两台主机之间 TCP/IP 通信是否正常，其实 **ping** 命令的原理就是 向对方主机发送UDP数据包，然后对方主机确认收到数据包，如果数据包是否到达的消息及时反馈回来，那么网络就是通的

<!-- more -->



## 区别

| TCP                                                          | UDP                                                          |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Transmission Control Protocol 传输控制协议                   | User Data Protocol 用户数据报协议                            |
| 面向**连接**的协议，提供**可靠**的通信传输                   | 面向**无连接**的协议，不提供差错恢复、数据重传，**安全性差** |
| 面向**字节流**（把上面应用层交下来的数据看成一连串无结构的字节流） | 面向**报文**（发送方的UDP对应用程序交下来的报文，在添加首部后就向下交付给IP层。既不拆分，也不合并，而是保留报文的边界） |
| **首部开销**20字节                                           | **首部开销**8字节                                            |
| 全双工，每一条连接只能是**点到点**                           | 支持**一对一、一对多、多对一和多对多**的交互通信             |
| 有**拥塞控制**，可以进行丢包的重发、对次序乱掉的分包进行顺序控制 | **没有拥塞控制**，会导致丢包、包的到达顺序错乱，但网络出现拥塞不会使 源主机 的发送速率降低（对实时应用很有用：IP电话，视频会议等） |
| **应用场景**<br />对当前网络通讯质量有要求，需要准确且安全地传输<br />1.浏览器使用的：HTTP <br/>2.QQ文件传输 | **应用场景**<br />对当前网络通讯质量要求不高，又要求网络通讯速度尽量快<br />1.QQ语音 <br/>2.QQ视频 <br/>3.TFTP |

#### TCP的包头结构

![图片](https://mmbiz.qpic.cn/mmbiz_png/gH31uF9VIibRn1aTIUxxEF1TrY9OM5OTa762ic1NDA46iatH8kzqyw8016xdURTbYVsZprQdWxES6sgL8nfTxmXIA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

- 源端口 16位
- 目标端口 16位
- 序列号 32位
- 回应序号 32位
- TCP头长度 4位
- reserved 6位
- 控制代码 6位
- 窗口大小 16位
- 偏移量 16位
- 校验和 16位
- 选项  32位(可选)

#### UDP的包头结构

![图片](https://mmbiz.qpic.cn/mmbiz_png/gH31uF9VIibRn1aTIUxxEF1TrY9OM5OTaIbgO1D2CX8SKKUQwujyAhMdrOgCHDJ9bpxHZd9GYEjmIzyhRc7Ys1w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

- 源端口 16位
- 目的端口 16位
- 长度 16位
- 校验和 16位
