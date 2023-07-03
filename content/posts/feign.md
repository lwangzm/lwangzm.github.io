---
title: "为什么在 feign 中的 @RequestParam  @PathVariable 注解需要有参数名称 ?"
date: 2023-07-03T21:29:37+08:00
reward: true
draft: false
description: "编译时会报错：RequestParam.value() was empty on parameter ()"
tags: [feign]
categories: [Java]
---

<!--more-->

在注解中加上名称就可以解决 `RequestParam.value() was empty on parameter ()` 的报错信息

但之前有疑惑为什么不加就不行，所以写下来我的翻译总结，可以直接看 stack overflow 上面的回答：

<https://stackoverflow.com/questions/44313482/feign-client-with-spring-boot-requestparam-value-was-empty-on-parameter-0>

<https://stackoverflow.com/questions/31845676/how-to-compile-spring-boot-applications-with-java-8-parameter-flag>

### 如何查找参数名称：

#### 一：

在编译时使用带有 `-parameters` 参数时，可以使用反射在编译后的字节码中查找参数名称

在使用 `maven` 编译项目时，旧的版本可以在 `maven-compiler-plugin` 插件中增加 `compilerArgs` 配置，我的3.5.1版本在加上的启动项目就不会报错了

新的版本不确定，应该是加上 `<parameters>true</parameters>` 配置

``` xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>版本号</version>
            <configuration>
                <source>${java.version}</source>
                <target>${java.version}</target>
                <compilerArgs>
                    <arg>-parameters</arg>
                </compilerArgs>
            </configuration>
        </plugin>
    </plugins>
</build>
```

#### 二：

没有 `-parameters` 参数时，从类文件的调试信息中查找变量名称

在把接口编译成类文件时，省略了参数名称的调试信息，所以就会报错查询不到。调试信息就是在打断点时使用，显示变量的名称等信息

因为接口中只有方法的定义没有具体实现，所以为了节省资源编译时直接省略了参数名称的调试信息，而编译类时就不会省略。那么在 feign 中的方法中不加注解的方法名称时，就会找不到名称值

打开 idea 的设置，在 `Java Compiler` 选项中，默认会勾选 `Generate debugging info` 选项，这个就是编译时生成调试信息，如果去掉这个选项，在方法的注解上不加参数名称时，请求就会报错







