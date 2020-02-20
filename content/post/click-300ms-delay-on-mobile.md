---
title: 关于移动端click 300ms延迟
date: 2019-05-29 11:28:26
tags:
    - html
    - css
    - 移动端
categories:
    - 前端
---

为了解决移动端适配的问题，提出了 viewport 的解决方案，基于 无障碍(accessibility)（需要代理）交互设计师为了更好的用户体验，特地提供了 双击缩放 的手势支持。移动浏览器 会在 touchend 和 click 事件之间，等待 300 - 350 ms，判断用户是否会进行双击手势用以缩放文字。

<!--more-->

### 解决方案

以下可以通过 hack 技巧，不添加 fastClick 也能修复延迟的问题

**禁用缩放**

-   Chrome on Android (all versions)
-   iOS 9.3

```html
<meta name="viewport" content="user-scalable=no" />
```

**或者**

```html
html { touch-action: manipulation; }
```

-   IE on Windows Phone

```html
html { touch-action: manipulation; // IE11+ -ms-touch-action: manipulation; // IE10 }
```

### 总结

如果你的业务需求，是只需要对 iOS 9.3 以上的 WKWebView 做适配，那么强烈建议你不去使用 `FastClick`，只需禁用缩放即可。
如果需要适配 iOS 9.3 以下 可以使用 `FastClick`，但引入 fastClick 在一些新版本的 ios 设备上会出现点击 input 不聚焦的问题。
