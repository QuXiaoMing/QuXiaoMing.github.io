---
title: Nginx 添加第三方模块报错
date: 2020-02-20 17:30:57
tags:
    - nginx
    - centOS
categories:
    - 运维
---

在 centOS 服务器为 `nginx` 添加 `SSL` 的时候，需要添加 ssl 模块。

**报错** `./configure: error: invalid option "–with-http_ssl_module"`

由于之前使用 `yum install nginx` 安装的 nginx, yum安装的是二进制文件，意思是已经编译好的nginx，它只有配置文件和二进制文件，没有源代码，所以你想加第三方模块，只能下载源码进行编译安装。

我们可以通过，获取yum安装的Nginx编译参数，之后使用同一版本的源代码进行编译，之后替换生成文件就可以了。

<!--more-->

1. 通过 `nginx -V` 就可以看到编译参数:

```bash
./configure --prefix=/etc/nginx \
            --sbin-path=/usr/sbin/nginx \
            --conf-path=/etc/nginx/nginx.conf \
            ...
```

2. 下载对应的源码
```bsah
cd /opt
wget http://nginx.org/download/nginx-1.12.1.tar.gz
```

3. 增加对应的模块
```bash
/configure --prefix=/etc/nginx \
            --sbin-path=/usr/sbin/nginx \
            --conf-path=/etc/nginx/nginx.conf \
            ...
            --add-module=../headers-more-nginx-module
```

4. 编译
```bash
make && make install
```

5. 替换
```bash
cp /usr/sbin/nginx /usr/sbin/nginx.bak #备份
cp /opt/nginx-1.12.1/objs/nginx /usr/sbin/nginx #替换
systemctl restart nginx #重启 nginx 服务
```