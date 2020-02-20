---
title: JSONP原理及简单实现
date: 2018-08-30 01:40:13
tags:
    - javascript
categories: 
    - 前端
---

`JSONP` 全称 JSON with Padding，用于解决 AJAX 跨域问题的一种方案。

由于同源策略的限制，浏览器只允许 XmlHttpRequest 请求当前源（域名、协议、端口）的资源，而对请求 script 资源没有限制。通过请求 script 标签实现跨域请求，然后在服务端输出 JSON 数据并执行回调函数，这种跨域的数据的方式被称为 JSONP。

<!-- more -->

# 实现原理

1.首先在客户端注册一个 callback 方法，放到 window 对象上，如：

```
callbackFunction (json) {
    console.log(JSON)
}
```

然后把 callback 的名字（callbackFunction）传给服务器。

2.服务器先生成 JOSN 数据。

3.将 JOSN 数据直接以入参的方式，放置到 function 中，这样就生成了一段 js 语法的文档（如 callbackFunction(JOSN)），返回给客户端。

4.客户端浏览器，将返回的 JS 标签插入 DOM，解析 script 标签后，会执行 callbackFunction(JOSN)。

通过这种方式，即可实现跨域获取数据。

# Code

```javascript
import {JSONP} from './JSONP';
// 调用
JSONP({
    url: 'url',
    data: {
        key1: 'key1'
    },
    callback(data) {
        // data 是服务端返回的数据
    }
});
```

```javascript
// JSONP.js
let JSONP = (config = {}) => {
    let {data, url, callback} = config;
    // 拼接请求Url
    if (!url) throw new Error('url is required!');
    let name = `id_${new Date().getTime()}_${Math.random()
        .toString()
        .substr(2)}`;
    let srcUrl = getSrcUrl(url, {
        data,
        callback: name
    });

    // 插入Script标签
    let script = document.createElement('script');
    script.type = 'text/javascript';
    script.src = srcUrl;
    script.id = name;

    // CallBack 放到 window 对象，调用后销毁
    window[name] = function(json) {
        // 执行这个函数后，要销毁这个函数
        window[name] = undefined;
        // 获取这个script的元素
        var elem = document.getElementById(name);
        // 删除head里面插入的script，这三步都是为了不影响污染整个DOM啊
        removeElem(elem);
        // 执行传入的的函数
        callback && typeof callback === 'function' && callback(json);
    };

    var head = document.getElementsByTagName('head');
    if (head && head[0]) {
        head[0].appendChild(script);
    }
};

let getSrcUrl = (url, data) => {
    let _url = url + (url.indexOf('?') === -1 ? '?' : '&');
    let ret = '';
    if (typeof data === 'string') {
        ret = data;
    } else if (typeof data === 'object') {
        for (let key in data) {
            ret += '&' + key + '=' + encodeURIComponent(data[key]);
        }
    }
    ret = ret.substr(1);
    return _url + ret;
};

let removeElem = elem => {
    let parent = elem.parentNode;
    if (parent && parent.nodeType !== 11) {
        parent.removeChild(elem);
    }
};

export default JSONP;
```

# 注意

1. 在 jquery 中，JSOP 被封装在 `$.ajax()` 方法中，但是他的实现原理与 `ajax` 完全不同。
2. JSONP 的兼容性较好，在更加古老的浏览器中都可以运行，不需要 XMLHttpRequest 或 ActiveX 的支持。
3. JSONP 只支持 GET 请求而不支持 POST 等其它类型的 HTTP 请求。
4. JSONP 在调用失败的时候不会返回各种 HTTP 状态码（解决方法：添加 timeout 参数，虽然 JSONP 请求本身的错误没有被捕获，但是最终会因为超时而执行 error 回调）。
5. 在使用 JSONP 的时候必须要保证使用的 JSONP 服务必须是安全可信的。万一提供 JSONP 的服务存在页面注入漏洞，它返回的 javascript 都将被执行，若被注入这是非常危险的。
