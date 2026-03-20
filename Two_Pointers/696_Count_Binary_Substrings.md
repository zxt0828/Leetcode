# 696. Count Binary Substrings

**难度:** Easy

**标签:** Two Pointers / String

## 题目链接

[LeetCode 696](https://leetcode.com/problems/count-binary-substrings/)

## 代码（双指针解法）

```java
class Solution {
    public int countBinarySubstrings(String s) {
        int res = 0;
        for (int i = 0; i < s.length() - 1; i++) {
            if (s.charAt(i) != s.charAt(i + 1)) {
                int left = i;
                int right = i + 1;
                while (left >= 0 && right < s.length()
                    && s.charAt(left) == s.charAt(i)
                    && s.charAt(right) == s.charAt(i + 1)) {
                    res++;
                    left--;
                    right++;
                }
            }
        }
        return res;
    }
}
```

## 解题心得

### 双指针的起点不一定在数组开头！

这道题打破了一个固定思维：双指针不一定是从 index 0 或两端开始的。这道题的双指针是**从交界处出发，向两边扩展**。

**思路：**
1. 先找到 0 和 1 的交界处（相邻字符不同的位置）
2. 从交界处出发，left 往左走，right 往右走
3. 只要 left 一直是同一个字符、right 也一直是同一个字符，就能配出合法子串

**双指针起点的常见模式：**
- **从头部开始**：滑动窗口、两数之和（排序后）
- **从两端开始**：盛水最多的容器、接雨水
- **从中间/交界处开始**：本题、回文子串（从中心扩展）

关键是根据题意判断**从哪里开始移动指针最合理**。

### 另一种解法：分组计数（O(n)）

```java
class Solution {
    public int countBinarySubstrings(String s) {
        int res = 0, prev = 0, cur = 1;
        for (int i = 1; i < s.length(); i++) {
            if (s.charAt(i) == s.charAt(i - 1)) {
                cur++;
            } else {
                prev = cur;
                cur = 1;
            }
            if (prev >= cur) res++;
        }
        return res;
    }
}
```

把连续相同字符分组，相邻两组取 min 就是能配出的子串数。

## 易错点

- 暴力解法（双重循环统计0和1个数）会把不连续的也算进去，比如 `"0110"` 里 0 和 1 个数相等但 0 不连续，不合法
- 双指针扩展时要同时检查**不越界**和**字符跟交界处一致**，四个条件缺一不可

## 犯过的错

1. 一开始用暴力双重循环，只统计 0 和 1 的个数是否相等，没有检查是否各自连续，导致 Wrong Answer
