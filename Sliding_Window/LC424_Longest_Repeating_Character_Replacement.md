# LC 424. Longest Repeating Character Replacement

## 题目信息

- **难度**：Medium
- **标签**：滑动窗口、哈希表
- **链接**：[NeetCode 150](https://neetcode.io/problems/longest-repeating-substring-with-replacement)

## 题意概述

给定一个只含大写字母的字符串 `s` 和整数 `k`，最多可以将 `k` 个字符替换为任意大写字母，求替换后**最长的只含单一字符的子串长度**。

## 核心思路

### 关键公式

```
需要替换的字符数 = 窗口长度 - 窗口内出现次数最多的字符次数
即：(right - left + 1) - max
```

- 如果 `≤ k`：窗口合法，可以尝试扩大
- 如果 `> k`：窗口非法，需要处理

### 为什么保留出现最多的字符？

因为替换次数有限，保留最多的、替换最少的，才能让窗口尽可能长。

## 解法：滑动窗口（if 写法，窗口只增/平移）

```java
class Solution {
    public int characterReplacement(String s, int k) {
        HashMap<Character, Integer> count = new HashMap<>();
        int left = 0;
        int max = 0;  // 窗口内单个字符的最大出现次数（只增不减）
        int res = 0;
        for (int right = 0; right < s.length(); right++) {
            count.put(s.charAt(right), count.getOrDefault(s.charAt(right), 0) + 1);
            max = Math.max(max, count.get(s.charAt(right)));
            if ((right - left + 1) - max > k) {
                count.put(s.charAt(left), count.get(s.charAt(left)) - 1);
                left++;
            }
            res = Math.max(res, right - left + 1);
        }
        return res;
    }
}
```

## 易错点 & 关键细节

### 1. 为什么用 `if` 而不是 `while`？

- 我们只关心**有没有更长的合法窗口**，不关心更短的
- 已经找到长度 10 的合法窗口后，就不需要再考虑长度 8、9 的窗口了
- 所以窗口**只扩大或平移，永远不缩小**
- 每轮 right 只右移 1，窗口最多非法超出 1，`if` 缩一次就够了

### 2. `max` 为什么只增不减？

- `max` 记录的是历史最大值，不是当前窗口的精确最大值
- 当 `max` 没被刷新时，窗口不会扩大（只会平移），不会产生更优解
- 只有 `max` 被刷新（变大）时，窗口才有可能扩大，才可能更新 `res`
- 所以不精确的 `max` 不影响最终答案的正确性

### 3. while 写法的对比

```java
while ((right - left + 1) - max > k) {
    count.put(s.charAt(left), count.get(s.charAt(left)) - 1);
    left++;
    // 必须重新计算真实的 max
    max = 0;
    for (int val : count.values()) {
        max = Math.max(max, val);
    }
}
```

- while 写法缩窗口后，max 可能不准，需要**重新遍历 map** 计算
- 虽然重算 max 是 O(26) 常数级，但做了很多无用功（缩到比 res 小的窗口没有意义）

## 复杂度

| | 时间 | 空间 |
|---|---|---|
| if 写法 | O(n) | O(1)（最多 26 个 key） |
| while 写法 | O(26n) → O(n) | O(1) |

## 模式总结

这道题属于**滑动窗口 + 窗口只增不缩**的优化模式：

1. 右指针扩大窗口
2. 判断窗口合法性（替换次数是否 ≤ k）
3. 非法时左指针只移一步（平移），而非缩到合法
4. 关键变量（max）只增不减，利用"答案单调性"省去精确维护的开销
