---
title: "org.artofsolving.jodconverter.office.OfficeException: office process died with exit code"
date: 2023-06-03T17:42:44+08:00
reward: true
draft: false
description: "使用 kkfileview 预览文件报错"
tags: [kkfileview]
categories: [Java]
---

<!--more-->

### 遇到的问题：

使用kkFileView做预览服务时，在Linux中部署完成后，点击预览后提示：org.artofsolving.jodconverter.office.OfficeException: office process died with exit code。

### 原因：

把打包好的kkfileview.jar.tar文件上传到服务器上解压后，我没有直接执行install.sh脚本，服务器不能执行脚本中的命令

自己下载好LibreOffice文件后上传，然后按照install.sh中的命令执行

在执行`yum groups install "X Window System"`时，会提示失败信息，然后继续执行了install.sh文档中的下面的命令

### 解决方法：

进入到libreoffice的program目录：

```shell
cd /opt/libreoffice7.3/program
```

执行：

```shell
./soffice --version
```

如果有问题就会提示某些库找不到，然后需要单独安装这个库所在的软件包，安装完成后

继续执行 `./soffice --version` 命令，直到看到版本号信息，再次预览文件就没有问题了

### 缺少的包：

我这里提示出来缺少的库是：libcups.so.2 和 libcairo.so.2，需要安装包含这个库的软件包

而对应的软件包分别是cups-libs和cairo，所以需要执行：

```shell
yum install -y cups-libs
yum install -y cairo
```



