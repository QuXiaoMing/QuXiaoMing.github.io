---
title: Electron autoUpdate 配置自动更新
date: 2018-2-10 10:20:01
tags:
    - electron
categories: 
    - 前端
---

网上关于 Electron 应用自动更新的文章并不多，许多都已经过时，且较为繁琐。
本文将介绍，通过 `Electron-Vue` + `autoUpdater` + `electron-release-server` 快速搭建自动更新服务。

<!--more-->

## 1 配置版本管理服务器

自动更新需要一个服务器存放 Electron 应用的安装包。本地应用在启动时检测服务器上是否有新版本。

这里选择的是 `electron-release-server` 作为托管的 electron 应用程序的发布服务器, 兼容自动更新器。

> Electron-release-server: A node web server which serves & manages releases of your Electron App, and is fully compatible with Squirrel Auto-updater (which is built into Electron).

### 1.1 安装数据库

默认使用 `PostgresSQL` 作为数据库。

```bash
# 安装
sudo apt-get install postgresql
#进入数据库
sudo -u postgres psql
# 设置管理员密码
postgres=# alter user postgres with password '123456';
#创建用户
CREATE ROLE electron_release_server_user ENCRYPTED PASSWORD '<PASSWORD>' LOGIN;
CREATE DATABASE electron_release_server OWNER "electron_release_server_user";
CREATE DATABASE electron_release_server_sessions OWNER "electron_release_server_user";
```

### 1.2 安装 Electron-release-server

```bash
git clone https://github.com/ArekSredzki/electron-release-server.git

cd electron-release-serve

# 修改配置文件
cp config/local.template config/local.js
# 配置用户名密码
vim config/local.js

npm install

npm start

```

访问 `1337` 端口就能看到管理程序了。

## 2 编译安装包

### 2.1 初始化项目

```bash
# 安装 vue-cli 和 脚手架样板代码
npm install -g vue-cli
vue init simulatedgreg/electron-vue my-project

cd my-project

# 安装依赖并编译你的程序
yarn # 或者 npm install
```

### 2.2 开启自动更新

安装依赖后，在主进程 main/index.js 里，增加自动更新代码。

在 `src/mian/index.js` 中设置更新地址

```js
// src/mian/index.js

import {autoUpdater} from 'electron-updater';

// 设置检测更新地址
// `Update URLs provided: /update/:platform/:version[/:channel]`
autoUpdater.setFeedURL('http://your.server.address:1337/update/windows_32/');

// 文件下载完成： 重启服务
autoUpdater.on('update-downloaded', () => {
    autoUpdater.quitAndInstall();
});

// 生产环境中检查更新
app.on('ready', () => {
    if (process.env.NODE_ENV === 'production') autoUpdater.checkForUpdates();
});
```

通过设置 `package.json` 的 `version` 来设置应用程序的版本。

版本号的设定需要符合语义化规则 [Semantic Versioning 2.0.0 ](https://semver.org/lang/zh-CN/)

### 2.3 编译文件

```bash
yarn run build # 或者 npm run build
```

编译完成后，在项目的 build 目录下会自动生成 `app-name Setup version.exe` 安装包。

## 3 上传安装包

在浏览器访问你的 Electron-release-server 应用地址，点击 `Add new Version`,选择对应的版本号，以及应用平台，上传 `Setup.exe`。

你还可以在服务器上对各个版本的应用进行管理。

到此，一个完整的 Electron 自动更新服务已经配置完成。

当本地应用版本低于对应服务器上应用版本时，程序会自动下载安装包，重启并更新应用。整个更新过程都在后台进行，并且不会删除用户数据。

## 4 踩坑

** 4.1 Cannot find channel "latest.yml" update **

更新地址设置错误，或者服务器配置失败都有可能导致这个错误。
**解决方法：设置正确的 setFeedURL： `/update/:platform/:version[/:channel]`**
在浏览器访问正确的 `feedURL/latest.yml` 可以看到最新版本的信息

> version: 1.8.0 releaseDate: Mon Feb 05 2018 17:19:31 GMT+0800 (CST) path: /download/1.8.0/windows_32/ctron-vue%20Setup%201.8.0.exe sha2: f5d0037020f2cea7254fb9ec2a4bb3c67250aa366f7aadf57a7cab9b38b124c0

** 4.2 Update info doesn't contain nor sha256 neither sha512 checksum **

```bash
Found version 1.8.0 (url: )
  update-available { version: '1.8.0',
    releaseDate: 'Mon Feb 05 2018 17:19:31 GMT+0800 (CST)',
    path: '/download/1.8.0/windows_32/ctron-vue%20Setup%201.8.0.exe',
    sha2: 'f5d0037020f2cea7254fb9ec2a4bb3c67250aa366f7aadf57a7cab9b38b124c0' }
  Downloading update from

  Error: Error: Update info doesn't contain nor sha256 neither sha512 checksum: {
    "url": "/download/1.8.0/windows_32/ctron-vue%20Setup%201.8.0.exe"
  }
```

更新信息不包含 sha256 和 ha512 校验

**原因：服务器返回的文件 `fileInfo` 只有 `sha2` 字段，并不包含 `sha256` 、 `sha512` 字段，导致报错。**

**临时解决办法： `node_modules\electron-updater\out\Provider.js` 找到下面代码，并注释掉。**

这样会关闭对文件的校验，并不影响文件的更新，但可能会带来一些安全隐患。

```js
if (fileInfo.sha2 == null && fileInfo.sha512 == null) {
    throw (0, (_builderUtilRuntime  _load_builderUtilRuntime()).newError)(`Update info doesn't contain nor sha256 neither sha512 checksum ${(0, (_builderUtilRuntime  _load_builderUtilRuntime()).safeStringifyJson)(fileInfo)}`, ERR_UPDATER_NO_CHECKSUM);
}
```

** 4.3 downgrade is disallowed electron **

不允许降级操作。

笔者发现在检查更新的时候，electron 会把 electron 的版本当作当前应用版本（package.json）。

当前应用是基于 electron@1.7.5，应用版本为 1.0.0，而服务器上应用版本低于 1.7.5，进而提示不允许降级操作。

这个 bug 是由于 electron 配置启动地址错误造成的。

**把 `/.electron-vue/dev-runner.js` 中 `startElectron` 路径 `path.join(__dirname, '../dist/electron/main.js')` 改成 `'.'`**

重启应用就能正确的获取应用版本了。

** 4.4 Critical dependency: the request of a dependency is an expression **

electron-debug 通过动态路径的方法来引入模块，但 webpack 并不支持动态路径。

作者认为这是 webpack 的原因，并且不想修复这个 issue 。。

**解决办法： 使用 electron-debug V1.4.0**

> That's caused by electron-debug trying to require a module using a dynamic path, which webpack doesn't support. Is not an electron-vue issue.
>
> Sorry, I'm not going to change my code because of webpack. See: webpack/webpack#196

---

写这篇文章主要是为了记录配置 electron 自动更新服务过程，以及遇到的一些问题。其中难免有一些疏忽和遗漏，遇到问题可在下方留言。
