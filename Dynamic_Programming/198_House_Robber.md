# 198. House Robber

- **难度**: Medium
- **分类**: Dynamic Programming
- **链接**: https://leetcode.com/problems/house-robber/

---

## 题目描述

你是一个小偷，沿一条街偷房子。每间房有一定金额，但不能偷相邻两间（会触发报警）。给定数组 `nums` 表示每间房的金额，求不触发报警的前提下能偷到的最大金额。

---

## 核心思路：动态规划

### dp 数组的定义

`dp[i]` = 从第 0 间房到第 i 间房，能偷到的**最大金额**。注意不是"一定偷第 i 间"，而是在前 i+1 间房中做出最优选择后的最大收益。

### 状态转移

对于第 i 间房，只有两个选择：

- **偷第 i 间**：第 i-1 间不能偷 → `dp[i-2] + nums[i]`
- **不偷第 i 间**：保持之前最优 → `dp[i-1]`

```
dp[i] = Math.max(dp[i-1], dp[i-2] + nums[i])
```

### dp 数组的单调性

`dp[i]` 只会递增或持平，不会变小。因为 `dp[i] = max(dp[i-1], ...)` ，至少等于 `dp[i-1]`。直觉上：多一间房子最差就是不偷它，收益不可能因为多了选择而减少。

---

## 手动模拟：[2, 7, 9, 3, 1]

```
dp[0] = 2         前1间 [2]，偷它，最多 2
dp[1] = max(2, 7) = 7    前2间 [2,7]，选 7
dp[2] = max(7, 2+9) = 11   前3间 [2,7,9]，选 2+9 跳过 7
dp[3] = max(11, 7+3) = 11  前4间 [2,7,9,3]，不偷 3 更划算
dp[4] = max(11, 11+1) = 12 前5间 [2,7,9,3,1]，偷 1 加上 dp[2]=11

答案：12（偷第 0、2、4 间：2 + 9 + 1）
```

---

## 代码：dp 数组版

```java
class Solution {
    public int rob(int[] nums) {
        if (nums.length == 1) return nums[0];

        int[] dp = new int[nums.length];
        dp[0] = nums[0];
        dp[1] = Math.max(nums[0], nums[1]);

        for (int i = 2; i < nums.length; i++) {
            dp[i] = Math.max(dp[i - 1], dp[i - 2] + nums[i]);
        }

        return dp[nums.length - 1];
    }
}
```

---

## 代码：空间优化版（推荐背诵）

`dp[i]` 只依赖 `dp[i-1]` 和 `dp[i-2]`，用两个变量替代整个数组，且不需要处理边界 case：

```java
class Solution {
    public int rob(int[] nums) {
        int prev2 = 0, prev1 = 0;

        for (int num : nums) {
            int curr = Math.max(prev1, prev2 + num);
            prev2 = prev1;
            prev1 = curr;
        }

        return prev1;
    }
}
```

---

## 复杂度分析

| | dp 数组版 | 空间优化版 |
|---|---|---|
| 时间 | O(n) | O(n) |
| 空间 | O(n) | O(1) |

---

## 易错点 & 复习提醒

- **dp[1] 的初始化是 `max(nums[0], nums[1])`，不是 `nums[1]`**：前两间房要选大的那个，不是直接取第二间。
- **空间优化版中变量更新顺序很重要**：必须先算 `curr`，再更新 `prev2 = prev1`，最后 `prev1 = curr`。顺序错了值会被覆盖。
- **dp 的含义是"前 i+1 间的最优解"，不是"一定偷第 i 间"**：这是理解递推公式的关键。

---

## 相关题目

- [213. House Robber II](https://leetcode.com/problems/house-robber-ii/) — 房子围成一圈，首尾不能同时偷
- [337. House Robber III](https://leetcode.com/problems/house-robber-iii/) — 房子排成二叉树结构
- [740. Delete and Earn](https://leetcode.com/problems/delete-and-earn/) — 本质是 House Robber 的变体
