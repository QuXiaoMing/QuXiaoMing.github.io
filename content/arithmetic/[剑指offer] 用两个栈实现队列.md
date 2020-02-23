---
title: "[剑指offer] 用两个栈实现队列"
date: 2020-02-23 18:14:33
url: "arithmetic/cong-wei-dao-tou-da-yin-lian-biao-lcof"
---

**描述** 用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 appendTail 和 deleteHead ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，deleteHead 操作返回 -1 )

示例 1：

输入：
```
["CQueue","appendTail","deleteHead","deleteHead"]
[[],[3],[],[]]
```
输出：`[null,null,3,-1]`

示例 2：

输入：
```
["CQueue","deleteHead","appendTail","appendTail","deleteHead","deleteHead"]
[[],[],[5],[2],[],[]]
```
输出：
```
[null,-1,null,null,5,2]
```
提示：
```
1 <= values <= 10000
```
最多会对 appendTail、deleteHead 进行 10000 次调用

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof

<!--more-->


## 解法1: 使用数组
```js
var CQueue = function() {
    this.stack = []
};

/** 
 * @param {number} value
 * @return {void}
 */
CQueue.prototype.appendTail = function(value) {
    this.stack.push(value)
};

/**
 * @return {number}
 */
CQueue.prototype.deleteHead = function() {
    if (!this.stack.length) {
        return -1
    }
    return this.stack.shift()
};

/**
 * Your CQueue object will be instantiated and called as such:
 * var obj = new CQueue()
 * obj.appendTail(value)
 * var param_2 = obj.deleteHead()
 */
```

执行用时 :
560 ms
, 在所有 JavaScript 提交中击败了
67.48%
的用户
内存消耗 :
75.7 MB
, 在所有 JavaScript 提交中击败了
100.00%
的用户

题目是使用2个栈实现，这里利用的数组的 shift 方法，显然是不符合规则的。

## 解法2

1. 利用栈后进先出的特点，我们先定义2个栈：stack1、stack2
2. 当我们执行 appendTail，插入 1，2，3，不妨先放进stack1，此时 stack1 = [1, 2, 3]。
3. 当我们要执行 deleteHead 的时候，我们需要把最先插入的出栈。此时可以利用栈后进先出的特点，先将 stack1 的所有元素一个个压如 stack2,
此时 stack1 = [], stack2 = [3, 2, 1], stack2最后入栈的就是最先 appendTail 如队列的 1，将 1 出栈即可。
4. 此时再执行 appendTail，先压入stack1; deleteHead 择调用 stack2.pop()，如果 stack2 为空，则再执行第三步，如果 stack1， stack2 都为空则返回 -1;

```js
var CQueue = function() {
    this.stack1 = []
    this.stack2 = []
};

/** 
 * @param {number} value
 * @return {void}
 */
CQueue.prototype.appendTail = function(value) {
    this.stack1.push(value)
};

/**
 * @return {number}
 */
CQueue.prototype.deleteHead = function() {
    if (!this.stack2.length) {
        if (!this.stack1.length) {
            return -1
        }
        while (this.stack1.length) {
            this.stack2.push(this.stack1.pop())
        }
    }
    return this.stack2.pop()
};

/**
 * Your CQueue object will be instantiated and called as such:
 * var obj = new CQueue()
 * obj.appendTail(value)
 * var param_2 = obj.deleteHead()
 */
```

