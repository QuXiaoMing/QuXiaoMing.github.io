---
title: "[剑指offer] 二维数组中的查找"
date: 2020-02-20 17:30:57
url: "arithmetic/er-wei-shu-zu-zhong-de-cha-zhao-lcof"
---

**描述**在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

## 示例:

现有矩阵 matrix 如下：

```bash
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```

给定 target = 5，返回 true。

给定 target = 20，返回 false。

限制：

```
0 <= n <= 1000

0 <= m <= 1000
```


来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof

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