---
title: Hexo使用不蒜子增加访问统计功能
date: 2019-09-17 17:30:57
tags: 
    - hexo
categories:
    - 博客
---

> 不蒜子：两行代码 搞定计数

在 `scripts.ejs` 插入不蒜子 js

<!-- more -->

```html
<!-- scripts.ejs 不蒜子 http://busuanzi.ibruce.info/  -->
<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
```

在需要显示的模版处添加

```html
<span id="busuanzi_container_site_pv"><i class="fa fa-eye"></i> <span id="busuanzi_value_site_pv"></span></span>
```
