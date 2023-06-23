---
title: "GC overhead limit exceeded"
date: 2023-06-23T17:55:20+08:00
reward: true
draft: false
description: "Java 进程内存溢出"
tags: [jvm]
categories: [Java]
---

<!--more-->

### 场景描述

系统页面出现报错信息，查看日志发现有多个接口调用同一个服务时失败了，判断不是接口的问题

被调用的服务中有一台机器有问题，在日志中出现了 GC overhead limit exceeded

### 问题处理

1. 先通过 jmap 命令导出了 dump 日志

   ``` shell
   jmap -dump:format=b,file=/tmp/heap.hprof pid
   ```

2. 重启服务，使生产环境恢复正常使用

3. 使用 JProfiler 分析日志，发现有多个相同的 sql 查询语句，查询的字段信息比较多，一个查询占用了 4M

   之前使用JProfiler 分析日志时，可以直接定位到具体开始调用的类，这次并没有给出具体的类

   在代码中搜索发现是有多个定时任务调用了同一个查询语句，只用到了几个字段，但是查询了很多字段

4. 新增接口，减少查询的字段；同时，增加 JVM 启动参数：

   -XX:+HeapDumpOnOutOfMemoryError  -XX:HeapDumpPath=/

5. 增加缓存是否可以解决，每次定时任务执行都是不同的数据，无法生效；

   是否和 xxl-job 的调度策略有关，定时任务指定的执行器是在服务 A 中，在服务 A 中通过 feign 调用了服务 B，B 出现了问题，和 xxl-job 的调度策略无关
