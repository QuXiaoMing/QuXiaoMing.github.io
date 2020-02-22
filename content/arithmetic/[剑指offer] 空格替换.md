---
title: "[剑指offer] 空格替换"
date: 2020-02-22 20:58:44
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


## 解法

```js
var replaceSpace = function(s) {
    return s.replace(/ /g, '%20')
};
```

执行用时 :
60 ms
, 在所有 JavaScript 提交中击败了
76.30%
的用户

内存消耗 :
33.9 MB
, 在所有 JavaScript 提交中击败了
100.00%
的用户