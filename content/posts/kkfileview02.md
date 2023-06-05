---
title: "Kkfileview 集成到项目中"
date: 2023-06-03T22:07:52+08:00
reward: true
draft: false
description: "集成 kkfileview 到项目中使用"
tags: [kkfileview]
categories: [Java]
---

<!--more-->

### 集成流程：

1. 项目文件的 url 下载链接和 kkfileview 要求的不一致，所以就在 `OnlinePreviewController` 类里面新增了一个接口。

   依然是传过去 url 参数，会根据 url 中包含的一些参数生成`kkfileview` 需要的 `FileAttribute` 类型，分别赋值名称类型等内容

2. 在 `WebConfig` 类中添加接口路径，对接口访问进行拦截处理

     <br/>

   ![01](/images/kkfileview0201.png)

   访问时会在 `BaseUrlFilter` 类中增加 **`baseUrl`** 参数，也就是下面👇代码

   三种生成方式，通过请求头里赋值参数、在配置文件中修改默认值、获取请求中 url

   ```java
   @Override
   public void doFilter(ServletRequest request, ServletResponse response, FilterChain filterChain) throws IOException, ServletException {
   
       String baseUrl;
       String configBaseUrl = ConfigConstants.getBaseUrl();
   
       final HttpServletRequest servletRequest = (HttpServletRequest) request;
       //1、支持通过 http header 中 X-Base-Url 来动态设置 baseUrl 以支持多个域名/项目的共享使用
       final String urlInHeader = servletRequest.getHeader("X-Base-Url");
       if (StringUtils.isNotEmpty(urlInHeader)) {
           baseUrl = urlInHeader;
       } else if (configBaseUrl != null && !ConfigConstants.DEFAULT_BASE_URL.equalsIgnoreCase(configBaseUrl)) {
           //2、如果配置文件中配置了 baseUrl 且不为 default 则以配置文件为准
           baseUrl = configBaseUrl;
       } else {
           //3、默认动态拼接 baseUrl
           baseUrl = request.getScheme() + "://" + request.getServerName() + ":" + request.getServerPort()
                   + servletRequest.getContextPath() + "/";
       }
       if (!baseUrl.endsWith("/")) {
           baseUrl = baseUrl.concat("/");
       }
   
       BASE_URL = baseUrl;
       request.setAttribute("baseUrl", baseUrl);
       filterChain.doFilter(request, response);
   }
   ```

   **`baseUrl`** 为空后，kkfileview 预览 pptx 文件时，会出错

3. kkfileview 会吧 pptx 文件转换成 pdf，里面的每一张幻灯片都会生成一个图片，会使用 **`baseUrl`** 作为图片访问地址的前缀。


### 遇到的问题：

1. 本地调试没问题，到服务器上出错：**`baseUrl`** 为空值导致，也就是前面所增加的接口拦截添加失效，没有进行拦截添加参数

   前端使用域名访问，然后通过 Apache 中配置的代理转发到服务的 ip 地址

   把过滤器中的接口前面加上 *，问题解决

   <br/>

   ![02](/images/kkfileview0202.png)

2. 如果使用多台服务，前端使用域名访问且开启了缓存，那么就需要修改 **`pdfUrl`**  的赋值逻辑，不然可能会出现在服务 1 中缓存了，然后在服务 2 中读取的问题

   在线预览的接口是：
   
   ```java
   @GetMapping( "/onlinePreview")
   public String onlinePreview(String url, Model model, HttpServletRequest req) {
       String fileUrl;
       try {
           fileUrl = WebUtils.decodeUrl(url);
       } catch (Exception ex) {
           String errorMsg = String.format(BASE64_DECODE_ERROR_MSG, "url");
           return otherFilePreview.notSupportedFile(model, errorMsg);
       }
       FileAttribute fileAttribute = fileHandlerService.getFileAttribute(fileUrl, req);
       model.addAttribute("file", fileAttribute);
       FilePreview filePreview = previewFactory.get(fileAttribute);
       logger.info("预览文件url：{}，previewType：{}", fileUrl, fileAttribute.getType());
       return filePreview.filePreviewHandle(fileUrl, model, fileAttribute);
   }
   ```
   
   不同的类型的 `filePreviewHandle` 方法实现不同，修改 Excel 类型的实现类在方法的倒数第二行，原代码：
   
   ```java
   model.addAttribute("pdfUrl", cacheFileName);
   ```
   
   修改为：
   
   ```java
   model.addAttribute("pdfUrl", baseUrl + cacheFileName);
   ```
   
   也就是加上 url 的前缀，这样在请求时就不会使用域名，直接使用的 ip 地址进行访问



