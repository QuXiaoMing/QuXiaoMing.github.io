---
title: "[剑指offer] 空格替换"
date: 2020-02-21 17:30:57
url: "arithmetic/ti-huan-kong-ge-lcof"
---

**描述** 请实现一个函数，把字符串 s 中的每个空格替换成"%20"。

 

示例 1：
```bash
输入：s = "We are happy."
输出："We%20are%20happy."
```

限制：
```
0 <= s 的长度 <= 10000
```
来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

<!--more-->


## 解法1: 暴力解

```js
var findNumberIn2DArray = function(matrix, target) {
    for (let i = 0; i< matrix.length; i++) {
        let row = matrix[i]
        for (let j = 0; j < row.length; j++) {
            if (row[j] === target) {
                return true
            }
        }
    }
    return false
};
```

执行用时 :
88 ms
, 在所有 JavaScript 提交中击败了
18.78%
的用户
内存消耗 :
37.4 MB
, 在所有 JavaScript 提交中击败了
100.00%
的用户

不推荐

## 解法2：减而治之

![剑指off](/images/er-wei-shu-zu-zhong-de-cha-zhao-lcof1.jpg)

```js
var findNumberIn2DArray = function(matrix, target) {
    let i = matrix.length - 1;
    let j = 0;
    while (i >= 0 && j < matrix[0].length) {
        let num = matrix[i][j]
        if (target < num ) {
            i--
        } else if (target > num) {
            j++
        } else {
            return true
        }
    }
    return false
};
```

执行用时 :
60 ms
, 在所有 JavaScript 提交中击败了
96.13%
的用户

内存消耗 :
36.4 MB
, 在所有 JavaScript 提交中击败了
100.00%
的用户