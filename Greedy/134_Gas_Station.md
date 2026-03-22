# 134. Gas Station

**难度:** Medium

**标签:** Greedy

## 题目链接

[LeetCode 134](https://leetcode.com/problems/gas-station/)

## 代码

```java
class Solution {
    public int canCompleteCircuit(int[] gas, int[] cost) {
        int totalTank = 0;
        int curTank = 0;
        int start = 0;

        for (int i = 0; i < gas.length; i++) {
            int diff = gas[i] - cost[i];
            totalTank += diff;
            curTank += diff;

            if (curTank < 0) {
                start = i + 1;
                curTank = 0;
            }
        }

        return totalTank >= 0 ? start : -1;
    }
}
```

## 解题心得

### 贪心策略：一次遍历同时完成两件事

**1. 判断有没有解：** `totalTank` 累加所有 `gas[i] - cost[i]`，如果最终 `totalTank < 0`，说明总油量不够跑一圈，返回 -1

**2. 找起点：** `curTank` 记录从当前起点出发的油箱剩余，一旦 `curTank < 0`，说明从当前起点走不到这里，把起点设为 `i + 1`，`curTank` 重置为 0

### 为什么 curTank < 0 时前面所有站都不行？

比如从 A 出发，经过 B、C，到 D 时油不够了。从 B 出发会不会更好？不会，因为 A 到 B 时油箱 ≥ 0（不然早就断了），从 B 出发少了这部分油，只会更差。所以 A 到 D 之间所有站都不可能是起点，直接跳到 D+1。

### 为什么不需要真的跑一圈验证？

- `start` 到末尾这段，`curTank` 一直 ≥ 0，没问题
- 开头到 `start-1` 这段整体亏油，但 `totalTank >= 0` 保证后半段攒的油足够补上前半段的亏损

## 易错点

- 不要想着暴力模拟每个起点跑一圈，O(n²) 会超时
- `totalTank` 和 `curTank` 的区别要搞清楚：`totalTank` 是全局判断有没有解，`curTank` 是局部判断当前起点行不行
- 题目保证解唯一，所以贪心找到的 `start` 就是唯一答案
