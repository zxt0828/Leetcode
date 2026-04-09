# 33. Search in Rotated Sorted Array

**难度:** Medium | **标签:** Binary Search, Array | **日期:** 2026-03-26

---

## 题目描述

给定一个升序排列但可能被**左旋转**过的整数数组 `nums`（元素互不相同），以及一个目标值 `target`，返回 `target` 的下标，不存在则返回 `-1`。要求时间复杂度 **O(log n)**。

```
示例: nums = [4,5,6,7,0,1,2], target = 0 → 输出: 4
```

---

## 思路分析

### 初始思路：找断点 + 两段二分

先线性扫描找到旋转的断点，然后对两段分别做标准二分。

```
[4, 5, 6, 7, 0, 1, 2]
          ↑ 断点
 左段 [4,5,6,7]   右段 [0,1,2]  → 各自二分
```

**问题：** 找断点用了 O(n) 线性扫描，整体 O(n)，不满足题目 O(log n) 的要求。

### 最优思路：一次二分

**核心观察：** 旋转数组只有一个转折点，所以每次取 mid，**必有一半是有序的**。

```
[4, 5, 6, 7, 0, 1, 2]
          mid
 ← 左半段有序 →  ← 右半段 →
```

**策略：先确定哪半段有序，再判断 target 是否在有序的那半段范围内。**

- 如果在 → 去有序那半段找
- 如果不在 → 去另一半找

### 为什么一定有一半有序？

旋转数组只有**一个转折点**。转折点不在左边就在右边，没有转折点的那半段一定有序。

---

## 代码

```java
class Solution {
    public int search(int[] nums, int target) {
        int left = 0, right = nums.length - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;

            if (nums[mid] == target) return mid;

            if (nums[mid] >= nums[left]) {
                // 左半段 [left, mid] 有序
                if (target >= nums[left] && target < nums[mid]) {
                    right = mid - 1;  // target 在有序的左半段
                } else {
                    left = mid + 1;   // target 在右半段
                }
            } else {
                // 右半段 [mid, right] 有序
                if (target > nums[mid] && target <= nums[right]) {
                    left = mid + 1;   // target 在有序的右半段
                } else {
                    right = mid - 1;  // target 在左半段
                }
            }
        }

        return -1;
    }
}
```

---

## 逻辑详解

### 第一步：判断 mid 在哪一段

```java
if (nums[mid] >= nums[left])
```

- `nums[mid] >= nums[left]` → mid 在**左半段**（较大的有序段），即 `[left, mid]` 有序
- 否则 → mid 在**右半段**（较小的有序段），即 `[mid, right]` 有序

> 用 `>=` 而不是 `>` 是为了处理 `left == mid` 的情况（只剩两个元素时）。

### 第二步：判断 target 是否在有序的那半段

以左半段有序为例：

```java
if (target >= nums[left] && target < nums[mid])
```

- target 在 `[nums[left], nums[mid])` 范围内 → 一定在左半段，`right = mid - 1`
- 否则 → target 不在有序的左半段，只可能在右半段，`left = mid + 1`

右半段有序时同理。

### 走一遍示例

`nums = [4,5,6,7,0,1,2], target = 0`

```
left=0, right=6, mid=3: nums[3]=7
  nums[3]=7 >= nums[0]=4 → 左半段有序
  target=0 在 [4,7) 内？ 否 → left = 4

left=4, right=6, mid=5: nums[5]=1
  nums[5]=1 >= nums[4]=0? 是 → 左半段有序
  target=0 在 [0,1) 内？ 是 → right = 4

left=4, right=4, mid=4: nums[4]=0 == target → return 4 ✓
```

### 走一遍易错用例

`nums = [3,5,1], target = 3`

```
left=0, right=2, mid=1: nums[1]=5
  nums[1]=5 >= nums[0]=3 → 左半段有序
  target=3 在 [3,5) 内？ 是 → right = 0

left=0, right=0, mid=0: nums[0]=3 == target → return 0 ✓
```

如果只比较 `nums[mid]` 和 `target`（不考虑有序范围），这个用例会出错。

---

## 复杂度

- **时间:** O(log n) — 每次排除一半
- **空间:** O(1) — 只用了几个变量

---

## 易错点总结

1. **不能线性找断点：** 虽然功能正确，但 O(n) 不满足题目要求
2. **判断有序用 `>=` 不是 `>`：** `left == mid` 时（两个元素）需要 `>=` 才能正确识别
3. **必须判断 target 是否在有序范围内：** 不能只比较 `nums[mid]` 和 `target`，要同时看 `nums[left]` 或 `nums[right]` 确定边界
4. **"不在有序那半段"就一定在另一半：** 转折点只有一个，不需要额外确认

---

## 相关题目

| 题号 | 题目 | 关联 |
|------|------|------|
| 153 | Find Minimum in Rotated Sorted Array | 和 `nums[right]` 比，找最小值 |
| 81 | Search in Rotated Sorted Array II | 有重复元素，需处理 `nums[mid] == nums[left]` |
| 33 | 本题 | 无重复，一次二分 O(log n) |
