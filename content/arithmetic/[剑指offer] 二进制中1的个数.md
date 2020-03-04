---
title: "[剑指offer] 二进制中1的个数"
date: 2020-03-04 17:21:26
url: "arithmetic/er-jin-zhi-zhong-1de-ge-shu-lcof"
---

**描述** 请实现一个函数，输入一个整数，输出该数二进制表示中 1 的个数。例如，把 9 表示成二进制是 1001，有 2 位是 1。因此，如果输入 9，则该函数输出 2。

示例 1：
```bash
输入：00000000000000000000000000001011
输出：3
解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为 '1'。
```

示例 2：
```bash
输入：00000000000000000000000010000000
输出：1
解释：输入的二进制串 00000000000000000000000010000000 中，共有一位为 '1'。
```

示例 3：
```bash
输入：11111111111111111111111111111101
输出：31
解释：输入的二进制串 11111111111111111111111111111101 中，共有 31 位为 '1'。
```

 

注意：本题与主站 191 题相同：https://leetcode-cn.com/problems/number-of-1-bits/

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
<!--more-->


## 解法1: 处理字符串

利用 `Number.prototype.toString` 将二进制数字转成字符串

```js
var hammingWeight = function(n) {
    return n.toString(2).split('').filter(e => e === '1').length
};
```
执行用时 :
64 ms
, 在所有 JavaScript 提交中击败了
94.39%
的用户

内存消耗 :
34.8 MB
, 在所有 JavaScript 提交中击败了
100.00%
的用户


## 解法2：逐位判断
根据 与运算 定义，设二进制数字 nn ，则有：

若 n \& 1 = 0n&1=0 ，则 nn 二进制 最右一位 为 00 ；

若 n \& 1 = 1n&1=1 ，则 nn 二进制 最右一位 为 11 。

根据以上特点，考虑以下 循环判断 ：

判断 nn 最右一位是否为 11 ，根据结果计数。

将 nn 右移一位（本题要求把数字 nn 看作无符号数，因此使用 无符号右移 操作）。

算法流程：

初始化数量统计变量 res = 0res=0 。

循环逐位判断： 当 n = 0n=0 时跳出。

res += n & 1 ： 即若 n \& 1 = 1n&1=1 ，则统计数 resres 加一。

n >>= 1 ： 将二进制数字 nn 无符号右移一位。

返回统计数量 resres 。

```
var hammingWeight = function(n) {
    let count = 0;
    let flag = 1;
    let times = 0;
    while (times++ < 32) {
        if (flag & n) {
            count += 1;
        }

        flag = flag << 1;
    }

    return count;
};
```
执行用时 :
72 ms
, 在所有 JavaScript 提交中击败了
72.90%
的用户
内存消耗 :
34.8 MB
, 在所有 JavaScript 提交中击败了
100.00%
的用户

方法3

巧用 `n&(n−1)`
`(n - 1)` 解析： 二进制数字 nn 最右边的 11 变成 00 ，此 11 右边的 00 都变成 11 。
`n & (n - 1)` 解析： 二进制数字 nn 最右边的 11 变成 00 ，其余不变。


![剑指off](/images/mian-shi-ti-15-er-jin-zhi-zhong-1de-ge-shu-wei-yun.png)


```js 
var hammingWeight = function(n) {
  let count = 0;
  while (n !== 0) {
    n = n & (n - 1);
    count++;
  }

  return count;
};
```
执行用时 :
68 ms
, 在所有 JavaScript 提交中击败了
85.98%
的用户
内存消耗 :
34.2 MB
, 在所有 JavaScript 提交中击败了
100.00%
的用户