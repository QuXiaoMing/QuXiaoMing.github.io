---
title: 用 Vue 编写抽象组件
date: 2019-05-06 11:37:44
tags:
    - vue
    - js
categories: 
    - 前端
---

看过 Vue 源码的同学可以知道，`<keep-alive>`、`<transition>`、`<transition-group>`等组件
组件的实现是一个对象，注意它有一个属性 `abstract` 为 `true`，表明是它一个`抽象组件`。

Vue 的文档没有提这个概念，在抽象组件的**生命周期**过程中，我们可以对包裹的子组件**监听的事件进行拦截**，也可以对子组件进行 **Dom 操作**，从而可以对我们需要的功能进行封装，而不需要关心子组件的具体实现。

<!-- more -->

**下面实现一个 `debounce` 组件，对子组件的 `click` 事件进行拦截**

核心代码如下：

```vue
<script>
import {get, debounce, set} from 'loadsh';

export default {
    name: 'debounce',

    abstract: true, //标记为抽象组件

    render() {
        let vnode = this.$slots.default[0]; // 子组件的vnode
        if (vnode) {
            let event = get(vnode, `data.on.click`); // 子组件绑定的click事件
            if (typeof event === 'function') {
                set(vnode, `data.on.click`, debounce(event, 1000));
            }
        }
        return vnode;
    }
};
</script>
```

使用

```vue
<debounce>
    <button @click="clickHandler">测试</button>
</debounce>
```

可以看到，按钮的 `click` 事件已经加上了去抖(debounce)操作。

**我们可以进一步对 `debounce` 组件进行优化。**

```vue
<script>
import {get, debounce, set} from '@/utils';

export default {
    name: 'debounce',

    abstract: true, //标记为抽象组件

    props: {
        events: String,
        wait: {
            type: Number,
            default: 0
        },
        options: {
            type: Object,
            default() {
                return {};
            }
        }
    },

    render() {
        let vnode = this.$slots.default[0]; // 子组件的vnode

        if (vnode && this.events) {
            let eventList = this.events.split(',');
            eventList.forEach(eventName => {
                let event = get(vnode, `data.on[${eventName}]`); // 子组件绑定的click事件
                if (typeof event === 'function') {
                    /**
                     * 加上debounce操作, 参数与 lodash 的debounce完全相同
                     */
                    set(vnode, `data.on[${eventName}]`, debounce(event, this.wait, this.options));
                }
            });
        }
        return vnode;
    }
};
</script>
```

使用

```vue
<debounce events="click" :wait="250" :options="{maxWait: 1000}">
    <button @click="clickHandler">测试</button>
</debounce>
```

**我们同样可以为输入框的 input 事件进行 debouce 操作**

```vue
<debounce events="input" :wait="250" :options="{maxWait: 1000}">
    <input @input="inputandler" placeholder="输入关键字进行搜索" />
</debounce>
```
