---
title: "mysql-collect-java 包版本升级遇到的问题"
date: 2023-07-15T17:30:58+08:00
reward: true
draft: false
description: "mysql-collect-java 从 8.0.12 升级到 8.0.27"
tags: [Connector/J]
categories: [Java]
---

<!--more-->

### 时间类型转换问题：

项目中有查询接口是使用 `Map` 对象接收数据库数据，使用了 `Object` 接收数据库的字符串和时间类型的字段

然后在 `Java` 代码中把 `Object` 转换成了 `Date` 类型

升级后出现转换失败，需要 `LocalDate`，修改为新建一个 Java 对象，对象里面设置 `Date` 类型，用Java` 对象替换 `Map` 对象

### 数据库连接时区问题：

服务在 `jdbc` 连接参数中没有指定 `serverTimezone` 参数，之前一直保存到数据库的时间是美国时间

数据已经保存了很多，属于历史遗留问题

升级后保存到数据库中的时间直接变成了中国时间，历史数据就会变成少了 13 或 14 小时

在 `jdbc` 连接参数中加上了`serverTimezone=America/Chicago` 参数，这样在保存数据进表时，依然是美国时间
