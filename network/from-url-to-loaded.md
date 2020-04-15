---
title: 从URL到页面加载完成
date: 2020-04-14
---

# 经典之问：从URL到页面加载完成的过程

## 前言

作为一个 web 开发相关的技术从业者，应该没有人会没有听说过这个问题，但就是这样被问烂的一个问题，至今仍然有不少面试官爱问它。既然大家都是有备而来，为什么面试官还要问这个问题呢？

当然是因为，这是一道上下限都非常高的题目，每个人都能说上些什么，但很少人能够面面俱到。

通过回答的逻辑性与关键知识点的理解深度，能够很好的考察面试者从基础到深入的对于 web 的总体了解程度，其中涉及到的各种各样的细节，每一个单独展开来讲都值得一谈。

根据回答的情况，还可以在某些地方抽出一些问题来，继续考察面试者的知识储备。

<br>

## 简答

### DNS 解析

- 输入 URL 地址
- 浏览器根据用户输入的 URL 地址，检查浏览器是否有**强缓存**
    - 如果有，则直接显示页面即可，没有则向下继续
- 进行 DNS 解析，按以下顺序查找是否具有 URL 所对应的 **ip 地址的缓存**
    1. 浏览器缓存
    2. 操作系统缓存
    3. hosts 文件
    4. 路由器缓存
    5. ISP（互联网服务提供商） DNS 缓存
- 都没有缓存，则进行 **DNS 查询**
    - 主机向本地域名服务器的查询一般采用**递归查询**
    - 本地域名服务器向根域名服务器的查询采用**迭代查询**

### 连接与请求

- 获得 IP 地址后，通过**三次握手**，建立 TCP 连接
    1. 客户端发送一个 SYN 包到服务器端口，进入 SYN_SEND 状态
    2. 服务器发回 SYN-ACK 的响应包，进入 SYN_RECV 状态
    3. 客户端发送 ACK 包，双方进入 ESTABLISHED 状态
- 客户端发送 **HTTP 请求**
- 服务端收到 HTTP 请求并解析，将请求处理交给相应的应用程序
    - 缓存验证信息
    - 获取资源
    - 数据库等操作
- 服务器响应报文通过 TCP 连接发给客户端
- 客户端浏览器**解析响应报文**
    - 检查状态码
        - 200 正常
        - 301/302 重定向
        - 304 协商缓存
        - 403/404/500 等错误
    - body 是否有压缩（如 GZip）
    - 根据文件类型做不同的处理
- 如果没有新的请求，则进行**四次挥手**，断开 TCP 连接
    1. 客户端发送 FIN 包给服务器，进入 FIN-WAIT-1 状态
    2. 服务器收到后通知应用程序准备关闭，返回 ACK 包，进入 CLOSE-WAIT 状态，只发送数据，不再接收请求
    3. 客户端收到 ACK 包后，进入 FIN-WAIT-2 状态，只接收数据，不再发送请求
    4. 等到服务器发送完所有数据后，发送 FIN-ACK 包，进入 LAST-ACK 状态
    5. 客户端收到后，返还 ACK 包，进入 TIME-WAIT 状态，等待 2MSL 后无异常则自动关闭
    6. 服务器收到该 ACK 包后，关闭服务器

### 资源解析

这里假设资源为 HTML 文档

- 浏览器进行 HTML 预解析，将需要额外请求的资源（JS、CSS、图片、视频等）进行对应的连接建立与请求发送
- 浏览器创建 Document 对象并解析 HTML，**构建 DOM 树**
    1. 将特定字符流解析为 HTML 标记
    2. 通过词法分析，将标记转换为对象并定义属性与规则
    3. 根据 HTML 标记之间的关系，将对象组成 DOM 树
- 在解析过程中，如果解析器遇到 **JS** 文件
    - 如无 async 与 defer 属性，则会阻塞 HTML 解析直到该文件下载并执行完成
    - 有 async 属性，则不阻塞解析，会异步下载并尽快执行 JS 代码，执行代码的时候暂停 DOM 解析
    - 有 defer 属性，也不阻塞解析，会异步下载但会在页面 DOMContentLoaded 事件发生后按前后顺序执行
- 解析样式文件，**构建 CSSOM 树**
    1. 字符流转换为标记流
    2. 根据标记创建节点
    3. 节点创建 CSSOM 树
- 将 DOM 树和 CSSOM 树合并，**构建渲染树**
    1. 从 DOM 树的根节点遍历所有可见节点
        - 不可见节点包括：
            - script、meta 等本身不可见的标签
            - 被 CSS 隐藏的节点，如 `display: none`
    2. 对每一个可见节点，找到恰当的 CSSOM 规则并应用
    3. 发布可视节点的内容和计算样式
    - 一边构建一边显示页面
- HTML 解析完成后，浏览器在 Document 对象上触发 **DOMContentLoaded 事件**
- 等待图片等内容加载以及脚本下载执行完成后，window 触发 **load 事件**
- 可能存在的 ajax 请求

<br>

## 细节

以上就是个人梳理的大致内容，或许有人会觉得全部都讲的话内容有点太多了，但是很多时候面试官想考这个问题，就是想听你如何尽可能地详细地讲出所有的细节，如果只是大概的过一遍那太肤浅，并不能体现出你的知识的广度与深度。

实际上因为内容覆盖面之广，所以许多地方还有一些值得细说的细节，值得讨论。

> 由于内容过长，于是将以下细节分别单独写篇文章，可以挑感兴趣的内容继续阅读。

- [浏览器缓存](cache.md)
- [DNS 协议](dns.md)
- [传输层协议](transport-layer-protocol.md)
- HTTP 协议
- SSL/TLS 协议
- HTML 解析与渲染