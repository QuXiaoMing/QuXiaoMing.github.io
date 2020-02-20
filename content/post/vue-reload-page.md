---
title: vue 刷新当前页面
date: 2019-06-03 10:57:26
tags:
    - vue
categories:
    - 前端
---

在不刷新页面的情况下，更新页面。这个 issue 两年前就提出来了，之前的文章里面也提供了一个 解决方案。在这里分享一下，我目前使用的新方案。

<!--more-->

设置 route

```js
{
  path:'/redirect/:path*',
  component: () =>import('@/views/redirect/index'),
}
```

跳转页面页面内容

```js
<script>
  export default {
    beforeCreate() {
      const { params, query } = this.$route
      const { path } = params
      this.$router.replace({ path: '/' + path, query })
    },

    render: function(h) {
      return h()
    }
  }
</script>
```

刷新方法

```js
refresh() {
    const { fullPath } = this.$route
    this.$router.replace({
       path: '/redirect' + fullPath
    })
},
```

当遇到你需要刷新页面的情况，你就手动重定向页面到 redirect 页面，它会将页面重新 redirect 重定向回来，由于页面的 key 发生了变化，从而间接实现了刷新页面组件的效果。
