---
title: 浏览器网页链接打开本地exe客户端程序
date: 2019-05-08 10:27:35
tags:
    - html
    - windows
categories: 
    - 前端
---

我们经常可以看到在浏览器打开客户端的场景：浏览器打开 QQ 聊天窗口，百度网盘打开网盘客户端下载等。

我们如何使用浏览器网页链接打开本地 exe 客户端程序？

<!-- more -->

**步骤如下**

1. 新建注册表文件 `szztClient.reg`, **客户端的名称和客户端的地址可以自己定义。**

```reg
[HKEY_CLASSES_ROOT\szztClient]
@="szztClientProtocol"
"URL Protocol"=""

[HKEY_CLASSES_ROOT\szztClient\DefaultIcon]
@="C:\\Program Files (x86)\\Google\\Chrome\\Application\\chrome.exe,1"

[HKEY_CLASSES_ROOT\szztClient\shell]
@=""

[HKEY_CLASSES_ROOT\szztClient\shell\open]
@=""

[HKEY_CLASSES_ROOT\szztClient\shell\open\command]
@="\"C:\\Program Files (x86)\\Google\\Chrome\\Application\\chrome.exe\" \"%1\""
```

2. 双击运行写入注册表
3. 在网页添加链接

```html
<a href="szztClient://">打开客户端</a>
```
