---
title: "连接 RabbiMQ 报错，rabbitmq beam.smp cpu 利用率过高"
date: 2023-07-04T21:47:29+08:00
reward: true
draft: false
description: ""
tags: [RabbitMQ]
categories: [Java]
---

<!--more-->

### 现象描述：

启动业务项目时，突然报错连接不了 `RabbitMQ`，查看日志分析后不是代码的问题，是 `RabbitMQ` 的问题

提示的错误信息是：` org.springframework.amqp.AmqpIOException: java.io.IOException`

### 处理：

先执行了 `top` 命令，显示 `RabbitMQ` 服务的 CPU 使用率占用过高，重启服务无法解决

查看 `RabbitMQ` 的提示信息发现是有一个插件出错，询问后，是有同事新安装了一个插件，但是插件所属的用户和之前的不一致，导致没有权限访问文件

其实 `Java` 进程中的错误日志已经给出了提示，IO 异常，就很可能是有些文件没有读取到
