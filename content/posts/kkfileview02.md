---
title: "Kkfileview 集成到项目中"
date: 2023-06-03T22:07:52+08:00
reward: true
draft: true
description: "集成 kkfileview 到项目中使用"
tags: [kkfileview]
categories: [Java]
---

<!--more-->

### 集成流程：

1. 项目文件的 url 下载链接和 kkfileview 要求的不一致，所以就在 `base` 类里面新写了一个接口。

   依然是传过去 url 参数，会根据 url 中包含的一些参数生成kkfileview 需要的 file 类型，分别赋值名称类型等内容(截图)

2. 在过滤器 `file` 中添加接口路径，对接口访问进行拦截处理并增加 **`baseUrl`** 参数

   也就是下面👇图中的内容，baseUrl 为空后，kkfileview 预览 pptx 文件时，会出错

3. kkfileview 会吧 pptx 文件转换成 pdf，里面的每一张幻灯片都会生成一个图片，会使用 `baseUrl` 作为图片访问地址的前缀。

   baseUrl 有三种生成方式，通过请求头里赋值参数、在配置文件中修改默认值、获取请求中 url

### 遇到的问题：

1. 本地调试没问题，到服务器上出错：使用服务器 ip 访问时会走到过滤器的方法里面，前端使用域名访问时然后通过 Apache 中配置的代理转发时，出现错误信息

   发现 `baseUrl` 是空值，没有走到正确的方面里面。把过滤器中的接口前面加上*，问题解决，如图：。

2. 如果使用多台服务，并开启了缓存，那么就需要修改 `baseUrl` 的赋值逻辑，不然可能会出现在服务 1 中缓存了，然后在服务 2 中读取不到的问题

   修改如下，这样域名访问就会变成 ip 地址访问



