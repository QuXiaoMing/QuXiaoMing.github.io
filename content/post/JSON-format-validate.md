---
title: JSON格式化校验的方法
date: 2018-07-22 16:40:13
tags:
    - javascript
categories: 
    - 前端
---

Javascript 目前没有提供判断 JSON 数据格式是否正确的方法。
在得到 JSON 数据后，若 JSON 格式错误，程序不能正常解析，抛出 `Uncaught SyntaxError` 将导致程序终止运行。在解析 JSON 数据之前进行校验，可避免这种情况的发生。

<!--more-->

## 什么是 JSON

JSON(JavaScript Object Notation, JS 对象标记) 是一种轻量级的数据交换格式。它基于 ECMAScript (w3c 制定的 js 规范)的一个子集，采用完全独立于编程语言的文本格式来存储和表示数据。简洁和清晰的层次结构使得 JSON 成为理想的数据交换语言。 易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率。

> -   JSON 是轻量级的文本数据交换格式
> -   JSON 独立于语言 \*
> -   JSON 具有自我描述性，更易理解
> -   JSON 比 XML 更小、更快，更易解析。

JSON 是 JS 对象的字符串表示法，它使用文本表示一个 JS 对象的信息，本质是一个**字符串**。

```javascript
let obj = new Object();
typeof JOSN.stringify(obj) === 'string'; // true
```

## JSON 格式化校验方法

对象与 JSON 可使用`JOSN.stringify(obj)`、`JOSN.parse(JSON)` 方法相互转换。

Javascript 目前没有提供判断 JSON 数据格式是否正确的方法。

在得到 JSON 数据后，若 JSON 格式错误，程序不能正常解析，抛出 `Uncaught SyntaxError` 将导致程序终止运行。在解析 JSON 数据之前进行校验，可避免这种情况的发生。

```javascript
// 判断JSON格式
function isJSON(str) {
    try {
        var obj = JSON.parse(str);
        return !!obj && (typeof obj === 'undefined' ? 'undefined' : typeof obj) === 'object';
    } catch (e) {
        /* ignore */
    }
    return false;
}

isJSON(''); // false
isJSON(123); // false
isJSON(NaN); // false
isJSON(null); // false
isJSON(undefined); // false
isJSON(new Object()); // false
isJSON('str'); // false
isJSON('{"key": "key", "value": "value"}'); // true
```
