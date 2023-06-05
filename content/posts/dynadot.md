---
title: "Dynadot 域名的 DNS 绑定 github"
date: 2023-06-05T20:26:18+08:00
reward: true
draft: false
description: "在 Dynadot 中设置 DNS 绑定到 github 个人博客"
tags: [dynadot]
categories: [博客]
---

<!--more-->

### 设置：

1. 直接在 dynadot 中设置，username  就是你的 github 用户名

<br/>

![dns](/images/dynadot.png)

2. github 中的 Custom domain 设置域名后，等待一会后，勾选下面的 Enforce HTTPS 选项

   完事需要等待 DNS 传播，大概 10 分钟左右就可以通过域名访问了

   可以关代理或者开代理试试，有一个时间差

### 参考链接：

​     [Github Docs Manage a custom domain ](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site?platform=windows)
