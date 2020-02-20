---
title: webpack 按需打包 loadsh
date: 2019-03-25 14:42:56
tags:
    - webpack
categories: 
    - 前端
---

`Loadsh` 是一个一致性、模块化、高性能的 JavaScript 实用工具库。在数据操作时，我们经常会用的 loadsh 封装好的一些工具方法，但是并不想把整个包打包进项目里面。
以下提供几个按需打包 lodash 的方法。

<!--more-->

**使用方法：**

```js
import {isEmpty, isObject, cloneDeep} from 'lodash';
```

**存在问题：**

当我们仅使用 lodash 其中一两个方法的时候，webpack 会将这个库打包进去，造成了不必要的资源浪费；

从 `vue-cli@3` 提供的打包分析工具可以看到，打包后 lodash 有 **70.7kb**。
![优化前](/img/webpack-lodash/before.png)

**解决办法有 3 种：**

### 1. 单独引入

Lodash 中的每个函数在 NPM 都有一个单独的发布模块，我们可以只引入我们需要的模块。

```js
import isEqual from 'lodash.isequal';
// 或者
import difference from 'lodash/difference';
```

在模块比较多的时候，这种写法比较麻烦，不推荐。

### 2.使用插件优化

使用 [lodash-webpack-plugin](https://github.com/lodash/lodash-webpack-plugin)

```bash
$ npm i --save lodash
$ npm i --save-dev lodash-webpack-plugin babel-core babel-loader babel-plugin-lodash babel-preset-env webpack
```

webpack.config.js

```js
var LodashModuleReplacementPlugin = require('lodash-webpack-plugin');
var webpack = require('webpack');

module.exports = {
    module: {
        rules: [
            {
                use: 'babel-loader',
                test: /\.js$/,
                exclude: /node_modules/,
                options: {
                    plugins: ['lodash'],
                    presets: [['env', {modules: false, targets: {node: 4}}]]
                }
            }
        ]
    },
    plugins: [new LodashModuleReplacementPlugin(), new webpack.optimize.UglifyJsPlugin()]
};
```

以上工作完成了，在每个你需要使用 lodash 函数的文件中只需要引用一次 lodash，即可调用任意函数而不会造成完全打包。

### 3.使用 lodash-es

tree-shaking 作为 rollup 的一个杀手级特性，能够利用 ES6 的静态引入规范，减少包的体积，避免不必要的代码引入，webpack2 也很快引入了这个特性。

要用到 tree-shaking，必然要保证引用的模块都是 ES6 规范的。lodash-es 是着具备 ES6 模块化的版本，只需要直接引入就可以。

```js
import {isEmpty, isObject, cloneDeep} from 'lodash-es';
```

可以看到，使用优化后，lodash 从原来的 **70.7kb** 减少到了**20.2kb**效果还是很明显的。
![优化后](/img/webpack-lodash/after.png)

**Referance**：

https://www.jb51.net/article/113235.htm

https://www.npmjs.com/package/lodash-webpack-plugin
