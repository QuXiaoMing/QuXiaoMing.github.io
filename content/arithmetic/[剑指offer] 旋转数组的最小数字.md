---
title: "[剑指offer] 旋转数组的最小数字"
date: 2020-03-02 12:20:55
url: "arithmetic/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/"
---

**描述** 把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个递增排序的数组的一个旋转，输出旋转数组的最小元素。例如，数组 [3,4,5,1,2] 为 [1,2,3,4,5] 的一个旋转，该数组的最小值为1。  

示例 1：
```bash
输入：[3,4,5,1,2]
输出：1
```
示例 2：

```bash
输入：[2,2,2,0,1]
输出：0
```

注意：本题与主站 154 题相同：https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
<!--more-->


## 解法1: 遍历

```js
var minArray = function(numbers) {
    let max = numbers[0]
    for(let i =0; i < numbers.length; i++) {
        let cur = numbers[i]
        if (cur < max) {
            return cur
        } else {
            max = cur
        }
    }
    return numbers[0]
};
```


执行用时 :
96 ms
, 在所有 JavaScript 提交中击败了
6.52%
的用户

内存消耗 :
34.5 MB
, 在所有 JavaScript 提交中击败了
100.00%
的用户

## 解法2：二分法
排序数组的查找问题首先考虑使用 `二分法` 解决，其可将遍历法的 `线性级别` 时间复杂度降低至 `对数级别` 。
```js
var minArray = function(numbers) {
    let l = 0; r =numbers.length -1;
    while (l < r) {
        let m = Math.floor((l + r) /2)
        let right = numbers[r]
        let middle = numbers[m]
        if (middle > right) {
            l = m + 1
        } else if (middle < right) {
            r = m
        } else {
            r --
        }
    }
    return numbers[r]
};
```

执行用时 :
56 ms
, 在所有 JavaScript 提交中击败了
97.83%
的用户
内存消耗 :
33.9 MB
, 在所有 JavaScript 提交中击败了
100.00%
的用户