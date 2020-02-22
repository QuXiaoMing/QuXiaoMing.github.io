---
title: "[剑指offer] 数组中重复的数字"
date: 2020-02-20 17:30:57
url: "arithmetic/shu-zu-zhong-zhong-fu-de-shu-zi-lcof"
---
## **题目** 找出数组中重复的数字。


**描述** 在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。

示例 1：
```bash
输入：
[2, 3, 1, 0, 2, 5, 3]
输出：2 或 3 
```

限制：

```bash
2 <= n <= 100000
```

<!--more-->

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof

## 解法1: ES6 数组方法

如果某个数的索引与最后出现位置索引不同，则这个数是重复的

```js
var findRepeatNumber = function(nums) {
    return nums.find((e, index) => nums.lastIndexOf(e) !== index)
};
```

执行用时 :2536 ms, 在所有 JavaScript 提交中击败了5.16%的用户

内存消耗 :40.3 MB, 在所有 JavaScript 提交中击败了100.00%的用户

使用数组方法较慢

## 解法2：hash

题目中有一个条件: 在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内，我们可以将当前的数值`x`作为 hash(数组的索引), 将x放到索引为x的位置。当遇到重复的数的时候，当前值所对应的位置已经有了一个相同的数，一定有 `nums[x] === x`。（因为第一次遇到 x 时已经将其交换至 nums[x] 处了）。利用以上方法，即可得到一组重复数字。）

```js
for (let i = 0; i< nums.length; i++) {
    let x = nums[i]
    if (i !== x && nums[x] === x) {
        return x
    } else {
        nums[x] = x
    }
    return -1
}
```

执行用时 :
76 ms, 在所有 JavaScript 提交中击败了83.87%的用户

内存消耗 :40.7 MB, 在所有 JavaScript 提交中击败了100.00%的用户