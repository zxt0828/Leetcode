# LeetCode 153 - Find Minimum in Rotated Sorted Array

## 题目描述

给你一个升序排列的数组，它被旋转了若干次（把末尾元素搬到开头）。找到数组中的最小值。

例如：`[1,2,3,4,5,6]` 旋转 4 次 → `[3,4,5,6,1,2]`

旋转后的数组是**两段有序**的结构：

```
[3, 4, 5, 6, | 1, 2]
 前半段(较大)   后半段(较小)
```

最小值就是后半段的第一个元素（即"断裂点"）。

---

## 解题思路：二分查找

### 二分查找 if 判断的通用思考框架

每道二分题写 if 的时候，问自己三个问题：

1. **我用什么条件来判断？**（比大小？算模拟结果？）
2. **满足条件时，答案在左边还是右边？**
3. **mid 本身能不能被排除？** 能就 `mid±1`，不能就 `mid`

### 问题1：用什么条件来判断？

比较 `nums[mid]` 和 `nums[right]`，判断 mid 在前半段还是后半段。

**为什么跟 nums[right] 比，而不是 nums[left]？**

用 `nums[left]` 会有无法区分的情况：

```
情况1: [3, 4, 5, 6, 1, 2]   nums[mid]=5 > nums[left]=3 → 最小值在右边
情况2: [1, 2, 3, 4, 5, 6]   nums[mid]=3 > nums[left]=1 → 最小值在右边？？（实际在左边）
```

同一个条件，两个不同的答案方向，无法区分。

而用 `nums[right]` 每种情况都能明确判断：

```
情况1: [3, 4, 5, 6, 1, 2]   nums[mid]=5 > nums[right]=2  → 最小值在右边 ✓
情况2: [5, 6, 1, 2, 3, 4]   nums[mid]=2 < nums[right]=4  → 最小值在左边 ✓
情况3: [1, 2, 3, 4, 5, 6]   nums[mid]=3 < nums[right]=6  → 最小值在左边 ✓
```

**根本原因**：旋转数组的断裂点（最小值）在右半部分。`nums[right]` 一定在断裂点的右侧或就是断裂点，所以跟它比可以稳定判断 mid 在断裂点的哪一边。而 `nums[left]` 可能在断裂点的左边也可能在右边，不够稳定。

### 问题2：满足条件时，答案在左边还是右边？

- `nums[mid] > nums[right]`：mid 在前半段（较大的部分），最小值一定在 mid 的**右边**
- `nums[mid] <= nums[right]`：mid 在后半段（较小的部分），最小值在 mid 的**左边，或者就是 mid 本身**

### 问题3：mid 能不能被排除？

- `nums[mid] > nums[right]` → mid 比 right 大，mid 肯定不是最小值 → **能排除** → `left = mid + 1`
- `nums[mid] <= nums[right]` → mid 可能就是最小值 → **不能排除** → `right = mid`

---

## 二分模板选择

有一个分支不能排除 mid（`right = mid`），所以必须用 `while(left < right)` 模板。

### 两种模板对比

| | `while(left <= right)` | `while(left < right)` |
|---|---|---|
| **退出条件** | `left > right` | `left == right` |
| **特点** | 每次都能排除 mid（`mid±1`） | 有一个分支不能排除 mid |
| **适用场景** | 找确定 target；二分答案（每个 mid 能明确判断对错） | mid 可能是答案、不能跳过的情况 |
| **例题** | 经典二分查找、吃香蕉 (875)、分割数组 (410) | 旋转数组找最小值 (153)、找第一个满足条件的位置 |

**怎么判断用哪个？** 问自己：每次看完 mid，能不能 100% 确定 mid 不是答案？能 → 模板一；不能 → 模板二。

---

## 代码

```java
class Solution {
    public int findMin(int[] nums) {
        int left = 0;
        int right = nums.length - 1;

        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[right]) {
                // mid在前半段，最小值在右边，mid肯定不是答案
                left = mid + 1;
            } else {
                // mid在后半段，mid可能是答案，不能排除
                right = mid;
            }
        }

        return nums[left]; // left == right，就是最小值
    }
}
```

**时间复杂度**：O(log n)  
**空间复杂度**：O(1)

---

## 走一遍例子

`nums = [3, 4, 5, 6, 1, 2]`

```
left=0, right=5, mid=2 → nums[2]=5 > nums[5]=2 → left=3
left=3, right=5, mid=4 → nums[4]=1 < nums[5]=2 → right=4
left=3, right=4, mid=3 → nums[3]=6 > nums[4]=1 → left=4
left=4, right=4 → 退出，return nums[4]=1 ✓
```

`nums = [4, 5, 0, 1, 2, 3]`

```
left=0, right=5, mid=2 → nums[2]=0 < nums[5]=3 → right=2
left=0, right=2, mid=1 → nums[1]=5 > nums[2]=0 → left=2
left=2, right=2 → 退出，return nums[2]=0 ✓
```

`nums = [1, 2, 3, 4, 5, 6]`（未旋转）

```
left=0, right=5, mid=2 → nums[2]=3 < nums[5]=6 → right=2
left=0, right=2, mid=1 → nums[1]=2 < nums[2]=3 → right=1
left=0, right=1, mid=0 → nums[0]=1 < nums[1]=2 → right=0
left=0, right=0 → 退出，return nums[0]=1 ✓
```
