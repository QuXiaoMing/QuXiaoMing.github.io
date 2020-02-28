---
title: "[剑指offer] 青蛙跳台阶问题"
date: 2020-02-28 17:51:55
url: "arithmetic/qing-wa-tiao-tai-jie-wen-ti-lcof"
---

**描述** 一只青蛙一次可以跳上1级台阶，也可以跳上2级台阶。求该青蛙跳上一个 n 级的台阶总共有多少种跳法。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

示例 1：

```bash
输入：n = 2
输出：2
```

示例 2：
```bsh
输入：n = 7
输出：21
```
提示：
0 <= n <= 100

注意：本题与主站 509 题相同：https://leetcode-cn.com/problems/fibonacci-number/

## 解法: 动态规划

思路同 [[剑指offer] 斐波那契数列](/arithmetic/fei-bo-na-qi-shu-lie-lcof/)

```js
var numWays = function (n) {
    if (n < 2) return 1

    let firstNum = numWays(0)
    let secondNum = numWays(1)
    let ret = 0
    for (let i = 2; i <= n; i++) {
        ret = (firstNum + secondNum) % 1000000007
        firstNum = secondNum
        secondNum = ret
    }
    return ret
};
```