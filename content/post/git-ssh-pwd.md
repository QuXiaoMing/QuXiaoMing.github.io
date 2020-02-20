---
title: 解决git pull,push每次都需要输入密码问题
date: 2019-05-05 14:22:16
tags:
    - git
categories: 
    - 笔记
---

如果我们 git clone 的下载代码的时候是连接的https://而不是git@git (ssh)的形式，当我们操作 git pull/push 到远程的时候，总是提示我们输入账号和密码才能操作成功，频繁的输入账号和密码会很麻烦。

<!--more-->

## 1. 本地保存帐号密码

git bash 进入你的项目目录，输入：

```bash
git config --global credential.helper store
```

然后你会在你本地生成一个文本，上边记录你的账号和密码。当然这些你可以不用关心。
然后你使用上述的命令配置好之后，再操作一次 git pull，然后它会提示你输入账号密码，这一次之后就不需要再次输入密码了。

## 2. 使用 SSH 连接

1. Git Bash 进入 ssh 目录

```bash
cd ~/.ssh
```

2. 生成 SSH key (文件名：id_rsa, id_rsa.pub)

```bash
 ssh-keygen -t rsa -C "xxxxxx@yy.com"  #建议填写自己真实有效的邮箱地址
```

3. 文本编辑器打开公钥 `id_rsa.pub` 复制内容，添加到 Github setting。
4. 测试

```bash
ssh -T git@github.com
```

> You've successfully authenticated, but GitHub does not provide shell access.

**说明配置成功**
