# 31. Next Permutation

- **难度**: Medium
- **分类**: Array / Two Pointers
- **链接**: https://leetcode.com/problems/next-permutation/

---

## 题目描述

给定一个整数数组 `nums`，将其重新排列为字典序中的下一个排列。如果已经是最大排列，则变为最小排列（升序）。要求 in-place 修改，O(1) 额外空间。

**通俗理解**：把数组看成一个数，用同样的数字找到"刚好比它大一点"的下一个数。

---

## 核心思路：三步法

关键直觉：要让数字变大但尽可能小地变大，应该改尽量靠右（低位）的位置，并且让改动幅度最小。

### Step 1：从右往左，找第一个下降的位置 i

从右向左扫描，找到第一个 `nums[i] < nums[i+1]` 的位置。i 右边全部是降序，意味着右边已经是这些数字能组成的最大排列了，不可能再变大，所以必须在 i 这个位置"动手"。

### Step 2：从右往左，找第一个大于 nums[i] 的元素 j，交换

在 i 右边的降序区间中，找到最小的那个大于 `nums[i]` 的值（从右往左第一个 > nums[i] 的就是）。交换 i 和 j。为什么选最小的？因为要让变大的幅度尽可能小。

### Step 3：将 i+1 到末尾 reverse

交换后 i 右边仍然是降序（最大排列）。我们已经通过 swap 让第 i 位变大了，所以右边要尽可能小——reverse 成升序就是最小排列。

### 特殊情况

如果 Step 1 找不到 i（整个数组是降序，如 `[3,2,1]`），说明已经是最大排列，直接 reverse 整个数组变成最小排列 `[1,2,3]`。

---

## 手动模拟：[1, 3, 5, 4, 2]

```
原始数组:   [1, 3, 5, 4, 2]

Step 1: 从右扫描 → 2<4<5 都是递增(降序部分)
        到 3<5，找到 i=1 (nums[1]=3)
        右边 [5,4,2] 已经是最大排列

Step 2: 右边找最小的 > 3 的数
        从右: 2≤3 跳过, 4>3 找到! j=3
        swap(1,3): [1, 4, 5, 3, 2]

Step 3: reverse i+1 到末尾
        [5,3,2] → [2,3,5]
        结果: [1, 4, 2, 3, 5]

验证: 13542 → 14235 ✓ (下一个排列)
```

---

## 代码

```java
class Solution {
    public void nextPermutation(int[] nums) {
        int n = nums.length;

        // Step 1: 从右往左找第一个 nums[i] < nums[i+1]
        int i = n - 2;
        while (i >= 0 && nums[i] >= nums[i + 1]) {
            i--;
        }

        // Step 2: 找右边第一个 > nums[i] 的，swap
        if (i >= 0) {
            int j = n - 1;
            while (nums[j] <= nums[i]) {
                j--;
            }
            swap(nums, i, j);
        }

        // Step 3: reverse i+1 到末尾
        reverse(nums, i + 1, n - 1);
    }

    private void swap(int[] nums, int a, int b) {
        int tmp = nums[a];
        nums[a] = nums[b];
        nums[b] = tmp;
    }

    private void reverse(int[] nums, int left, int right) {
        while (left < right) {
            swap(nums, left++, right--);
        }
    }
}
```

---

## 复杂度分析

| | 复杂度 | 说明 |
|---|---|---|
| 时间 | O(n) | 最多三次线性扫描 |
| 空间 | O(1) | in-place 操作 |

---

## 易错点 & 复习提醒

- **Step 1 的比较是 `>=` 不是 `>`**：相等的元素也要跳过，否则 `[1,5,1]` 这种 case 会出错。
- **Step 2 的比较也是 `<=`**：同理，要找严格大于 `nums[i]` 的元素。
- **找不到 i 的情况别忘了**：`i < 0` 时跳过 Step 2，直接 reverse 整个数组。
- **Step 3 为什么 reverse 就行，不用排序？** 因为 swap 不会改变 i 右边的降序性质。swap 后右边仍然是降序，reverse 一下就变成升序，等价于排序但只要 O(n)。

---

## 三步法背后的 Why

| 步骤 | 做了什么 | 为什么 |
|---|---|---|
| Step 1 | 找最右边能变大的位置 | 改低位影响最小 |
| Step 2 | 换上刚好比它大的数 | 保证变大幅度最小 |
| Step 3 | 右边 reverse 成升序 | 让剩余部分尽可能小 |

三步合在一起 = 找到比当前排列大的所有排列中最小的那个 = 下一个排列。

---

## 相关题目

- [46. Permutations](https://leetcode.com/problems/permutations/) — 生成所有排列（回溯）
- [47. Permutations II](https://leetcode.com/problems/permutations-ii/) — 有重复元素的全排列
- [556. Next Greater Element III](https://leetcode.com/problems/next-greater-element-iii/) — 本题的整数版本，思路完全一样
