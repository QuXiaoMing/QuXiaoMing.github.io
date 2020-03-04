---
title: "[剑指offer] 调整数组顺序使奇数位于偶数前面"
date: 2020-03-04 17:21:26
url: "arithmetic/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof"
---

**描述** 输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有奇数位于数组的前半部分，所有偶数位于数组的后半部分。

 

示例：
```bash
输入：nums = [1,2,3,4]
输出：[1,3,2,4] 
注：[3,1,2,4] 也是正确的答案之一。
```

提示：

```bash
1 <= nums.length <= 50000
1 <= nums[i] <= 10000
```

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。


<!--more-->


## 解法1: 数组方法

```js
/**
 * @param {number[]} nums
 * @return {number[]}
 */
var exchange = nums => nums.sort(e => e % 2 === 0)

// 或者

var exchange = function(nums) {
    const isOdd = num => num % 2 === 1
    return [...nums.filter(e => isOdd(e)), ...nums.filter(e => !isOdd(e))]
};
```
执行用时 :
180 ms
, 在所有 JavaScript 提交中击败了
19.70%
的用户

内存消耗 :
43.7 MB
, 在所有 JavaScript 提交中击败了
100.00%
的用户

## 解法2：首尾双指针

1. 定义头指针 left ，尾指针 right .
2. left 一直往右移，直到它指向的值为偶数
3. right 一直往左移， 直到它指向的值为奇数
4. 交换 nums[left] 和 nums[right].
5. 重复上述操作，直到 left == right.

```
/**
 * @param {number[]} nums
 * @return {number[]}
 */
var exchange = function(nums) {
    if (nums.length < 2) return nums
    const isOdd = num => num % 2 === 1
    let left = 0, right = nums.length -1
    while (left !== right) {
        let l = nums[left], r = nums[right]
        if (isOdd(l)) {
            left++
            continue
        }
        if (!isOdd(r)) {
            right--
            continue
        }
        nums[left] = r
        nums[right] = l
    }
    return nums
};
```

执行用时 :
108 ms
, 在所有 JavaScript 提交中击败了
90.15%
的用户


内存消耗 :
43.9 MB
, 在所有 JavaScript 提交中击败了
100.00%
的用户
