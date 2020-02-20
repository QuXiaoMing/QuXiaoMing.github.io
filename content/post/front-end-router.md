---
title: 前端路由原理解析和实现
date: 2019-05-17 11:14:26
tags:
    - js
categories: 
    - 前端
---

路由的概念来源于服务端，在服务端中路由描述的是 URL 与处理函数之间的映射关系。
在 Web 前端单页应用 SPA(Single Page Application)中，路由描述的是 URL 与 UI 之间的映射关系，这种映射是单向的，即 URL 变化引起 UI 更新（无需刷新页面）。

<!--more-->

前端路由的两种模式：

-   hash-router
-   history-router

### hash-router

> hash 属性是一个可读可写的字符串，该字符串是 URL 的锚部分（从 # 号开始的部分）。
> location.hash=anchorname

**hashchange**

> The `hashchange` event is fired when the fragment identifier of the URL has changed (the part of the URL beginning with and following the # symbol).
> URL 片段标识符（开头的 URL 的一部分和`#`号后的部分)发生改变，会触发 `hashchange` 事件。

**实现：**

1. 通过浏览器前进后退改变 url、通过<a>标签改变 url、通过 window.url，这几种情况改变 url。
2. 通过 hashchange 事件监听 URL 的变化，触发 hashchange 事件。
3. 在 hashchange 事件中实现路由切换。

```html
<body>
    <ul>
        <!-- 定义路由 -->
        <li><a href="#/home">home</a></li>
        <li><a href="#/about">about</a></li>

        <!-- 渲染路由对应的 UI -->
        <div id="routeView"></div>
    </ul>
</body>
<script>
    // 页面加载完不会触发 hashchange，这里主动触发一次 hashchange 事件
    window.addEventListener('DOMContentLoaded', onLoad);
    // 监听路由变化
    window.addEventListener('hashchange', onHashChange);

    // 路由视图
    var routerView = null;

    function onLoad() {
        routerView = document.querySelector('#routeView');
        onHashChange();
    }

    // 路由变化时，根据路由渲染对应 UI
    function onHashChange() {
        console.log('onHashChange', location.hash);
        switch (location.hash) {
            case '#/home':
                routerView.innerHTML = 'Home';
                return;
            case '#/about':
                routerView.innerHTML = 'About';
                return;
            default:
                return;
        }
    }
</script>
```

### history-router

-   history 提供了 `pushState` 和 `replaceState` 两个方法，这两个方法改变 URL 的 path 部分不会引起页面刷新
-   history 提供类似 `hashchange` 事件的 `popstate` 事件，但 `popstate` 事件有些不同：通过浏览器前进后退改变 URL 时会触发 `popstate` 事件，通过`pushState/replaceState`或`<a>`标签改变 URL 不会触发 `popstate` 事件。好在我们可以拦截 `pushState/replaceState`的调用和<a>标签的点击事件来检测 URL 变化，所以监听 URL 变化可以实现，只是没有 hashchange 那么方便。

```html
<body>
    <ul>
        <li><a href="/home">home</a></li>
        <li><a href="/about">about</a></li>

        <div id="routeView"></div>
    </ul>
</body>

<script>
    // 页面加载完不会触发 hashchange，这里主动触发一次 hashchange 事件
    window.addEventListener('DOMContentLoaded', onLoad);
    // 监听路由变化
    window.addEventListener('popstate', onPopState);

    // 路由视图
    var routerView = null;

    function onLoad() {
        routerView = document.querySelector('#routeView');
        onPopState();

        // 拦截 <a> 标签点击事件默认行为， 点击时使用 pushState 修改 URL并更新手动 UI，从而实现点击链接更新 URL 和 UI 的效果。
        var linkList = document.querySelectorAll('a[href]');
        linkList.forEach(el =>
            el.addEventListener('click', function(e) {
                e.preventDefault();
                history.pushState(null, '', el.getAttribute('href'));
                onPopState();
            })
        );
    }

    // 路由变化时，根据路由渲染对应 UI
    function onPopState() {
        switch (location.pathname) {
            case '/home':
                routerView.innerHTML = 'Home';
                return;
            case '/about':
                routerView.innerHTML = 'About';
                return;
            default:
                return;
        }
    }
</script>
```

参考：
[前端路由原理解析和实现](https://juejin.im/post/5cd8d609e51d456e7b372155)
