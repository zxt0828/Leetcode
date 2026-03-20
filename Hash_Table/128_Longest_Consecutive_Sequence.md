# 128. Longest Consecutive Sequence

**难度:** Medium

**标签:** Hash_Table

## 题目链接

[LeetCode 128](https://leetcode.com/problems/longest-consecutive-sequence/)

## 代码

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        int res = 0;

        HashSet<Integer> set = new HashSet<>();
        for (int i : nums) {
            set.add(i);
        }

        for (int num : set) {
            if (!set.contains(num - 1)) {
                int next = num;
                int count = 0;
                while (set.contains(next)) {
                    count++;
                    next++;
                }
                res = Math.max(res, count);
            }
        }

        return res;
    }
}
```

## 解题心得

- 把所有数扔进 HashSet，然后只从**序列起点**开始往后数
- 怎么判断是起点？`num - 1` 不在 set 里，说明它前面没有数了，它就是一段连续序列的第一个
- 比如 `[100, 4, 200, 1, 3, 2]`：只有 1、100、200 是起点，2、3、4 直接跳过

## 易错点

### 1. 每个元素都往后数 → 超时（第一次 TLE）
```java
// 错误：没有判断起点，每个元素都尝试往后数
for (int i = 0; i < nums.length; i++) {
    int next = nums[i];
    // ...
}
```
比如有 1,2,3,4，从 1 数到 4，又从 2 数到 4，又从 3 数到 4，大量重复计算。

**修复：** 加一行 `if (!set.contains(nums[i] - 1))` 只从起点开始数。

### 2. 遍历原数组而不是遍历 set → 超时（第二次 TLE）
```java
// 错误：遍历原数组，重复元素会重复检查
for (int i = 0; i < nums.length; i++) {
    if (!set.contains(nums[i] - 1)) { ... }
}
```
如果数组有大量重复元素（比如 `[3,3,3,3,3,...]`），同一个起点会被检查多次。

**修复：** 改成遍历 set，自动去重：
```java
for (int num : set) {
    if (!set.contains(num - 1)) { ... }
}
```

### 总结：两个优化缺一不可
1. **只从起点开始数**（跳过非起点元素）
2. **遍历 set 不遍历原数组**（避免重复元素重复检查）
