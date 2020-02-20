---
title: Vue 项目实现按钮级别权限管理
date: 2019-04-23 10:39:55
tags:
    - javascript
    - vue
categories: 
    - 前端
---

在项目中经常有需求要根据用户的权限对界面上的元素进行控制，这里介绍了一直简单的实现，仅供参考。

<!--more-->

当前用户的权限列表储存在 `store` 里，也可以是其他地方。

**指令**

```js
// src/directives/permission.js
import Vue from 'vue';
import store from '@/store';
import {get} from '@/utils';

// 是否有权限
const hasPermission = userPermission => {
    let userPermissionList = Array.isArray(userPermission) ? userPermission : [userPermission];
    // 当前用户的权限列表
    let permissionList = get(store, 'getters["user/permission"]', []);
    return userPermissionList.some(e => permissionList.includes(e));
};

// 指令
Vue.directive('per', {
    bind: (el, binding, vnode) => {
        if (!hasPermission(binding.value)) {
            el.parentNode.removeChild(el);
        }
    }
});

// 全局判断方法
Vue.prototype.$_has = hasPermission;
```

**使用方法**

在 `mian.js` 引入

```vue
<div v-per="[admin]">
    admin 可见
    是否为admin：{{$_has('admin')}}   //true
<div>
```
