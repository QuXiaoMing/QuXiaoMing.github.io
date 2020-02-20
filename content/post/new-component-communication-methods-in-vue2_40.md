---
title: Vue2.4组件间通信新姿势
date: 2017-07-23 13:08:36
tags:
    - javascript
    - vue
categories: 
    - 前端
---

Vue 应用在组件化之后，通常存在着 父子组件、兄弟组件、跨级组件 等组件关系，那么组件之间如何进行通信；Vue2.4 提供了两种新的组件通讯方法。

<!-- more -->

在 Vue 中，父子组件的关系可以总结为 props down、events up。

-   [父子组件通信](https://cn.vuejs.org/v2/guide/components.html#%E4%BD%BF%E7%94%A8-Prop-%E4%BC%A0%E9%80%92%E6%95%B0%E6%8D%AE)：父组件通过 props 向下传递数据给子组件
-   [子父组件通信](https://cn.vuejs.org/v2/guide/components.html#%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BA%8B%E4%BB%B6)：子组件通过 events 给父组件发送消息 -使用 $on(eventName) 监听事件
    -使用 $emit(eventName) 触发事件
-   [ 非父子组件通信](https://cn.vuejs.org/v2/guide/components.html#%E9%9D%9E%E7%88%B6%E5%AD%90%E7%BB%84%E4%BB%B6%E9%80%9A%E4%BF%A1)：使用一个空的 Vue 实例作为中央事件总线

在 Vue 2.4 版本引入了组件通讯的新方式。

## 1. 重新引入 .sync 修饰符

熟悉 Vue1.x 的朋友一定对`.sync`修饰器并不陌生。在 Vue1.x 中我们可能会需要对一个 prop 进行『双向绑定』。当一个 **子组件** 改变了一个 prop 的值时，这个变化也会 **同步到父组件中** 所绑定的值。

因为它破坏了『单向数据流』的假设，`.sync`在 2.0 版本被移除，引起了广泛的讨论。在 2.3.0 版本`.sync` 又回来了，不过与 1.x 不同。

这次只是原有语法的语法糖(syntax sugar)包装而成，其背后实现原理是，在组件上自动扩充一个额外的 `v-on` 监听器：

代码如下：

```html
<comp :foo.sync="bar"></comp>
```

会被扩充为：

```html
<child :bar="foo" @update:bar="e => foo = e"></child>
```

对于子组件，如果想要更新 foo 的值，则需要显式地触发一个事件，而不是直接修改 prop：

```javsscript
    this.$emit('update:bar', newValue)
```

## 2. $attrs 与 $listeners

多级组件嵌套需要传递数据时，通常使用的方法是通过 vuex。如果仅仅是传递数据，而不做中间处理，使用 vuex 处理，未免有点杀鸡用牛刀。Vue 2.4 版本提供了另一种方法，使用 v-bind="\$attrs", 将父组件中不被认为 props 特性绑定的属性传入子组件中，通常配合 interitAttrs 选项一起使用。

### 2.1 interitAttrs

在版本 2.4 之前，默认情况下父作用域的不被作为 props 特性绑定的属性，将会作为普通的 HTML 属性，应用在跟元素上。

```html
 // parent.vue
<template>
    <child-commpent :foo="f" :boo="b"></child-comment>
</template>

<script>
const childComment = () => import('./childCom.vue')
export default {
    data () {
      return {
        f: 'Hello world!'
        b: 'Hello Vue!'
      }
    }
}
</script>
```

```html
// childComment.vue
<template>
    <div>{{ foo }}<div>
</template>

<script>
export default {
    props: ['foo'] //父作用域的boo不被作为props绑定
}
</script>
```

最后会被渲染为：

```html
//boo会作为普通的 HTML 属性，应用在跟元素上。
<div boo="Hello Vue!">Hello world!</div>
```

设置 interitAttrs 为 false，之后，不会应用到跟元素上。

```html
// childCom.vue
<template>
    <div>{{ foo }}</div>
</template>

<script>
    export default {
        props: ['foo'],
        inheritAttrs: false
    };
</script>
```

最后会被渲染为：

```html
//boo会作为普通的 HTML 属性，应用在跟元素上。
<div>Hello world!</div>
```

## 2.2 $attrs, $listeners

在 Vue 2.4 版本,配合 interitAttrs 选项， **父组件中未被 props(v-on)绑定的属性(事件)** 可以在子组件中，通过 `$attrs`， `$listeners` 获取。

```html
// demo.vue
  <template>
    <div>
      <child-com :foo="foo" :boo="boo" :coo="coo" :doo="doo"></child-com>
    </div>
  </tempalte>
  <script>
  const childCom = () => import('./childCom1.vue')
  export default {
    data () {
      return {
        foo: 'Hello World!',
        boo: 'Hello Javascript!',
        coo: 'Hello Vue',
        doo: 'Last'
      }
    },
    components: { childCom }
  }
  </script>
```

```html
// childCom1.vue
<template>
    <div>
        <p>foo: {{ foo }}</p>
        <p>attrs: {{ $attrs }}</p>
        <child-com2 v-bind="$attrs"></child-com2>
    </div>
</template>
<script>
    const childCom2 = () => import('./childCom2.vue');
    export default {
        props: ['foo'], // foo作为props属性绑定
        inheritAttrs: false,
        created() {
            console.log(this.$attrs); // { boo: 'Hello Javascript!', coo: 'Hello Vue', doo: 'Last' }
        }
    };
</script>
```

```html
// childCom2.vue
<template>
    <div>
        <p>boo: {{ boo }}</p>
        <p>attrs: {{ $attrs }}</p>
        <child-com3 v-bind="$attrs"></child-com3>
    </div>
</template>

<script>
    const childCom3 = () => import('./childCom3.vue')
    export default {
      props: ['boo'] // boo作为props属性绑定
      inheritAttrs: false,
      created () {
        console.log(this.$attrs) // { coo: 'Hello Vue', doo: 'Last' }
      }
    }
</script>
// childCom3.vue // ...
```

渲染为：

![$attrs 与 $listeners](/img/new-component-communication-methods-in-vue2_40/render.png)

## 小结

1. 在 Vue2.0 被移除的 `.sync` 被重新加入到 2.4 版本，不同的是需要显式地触发一个事件，而不是直接修改 prop。
2. Vue2.4 提供了 `$attrs`, `$listeners` 来传递数据与事件，跨级组件之间的通讯变得更简单。
