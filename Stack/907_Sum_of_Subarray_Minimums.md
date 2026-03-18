# 907. Sum of Subarray Minimums

**刷题日期:** 2026-03-18

**难度:** Medium

**标签:** Stack (单调栈)

## 题目链接

[LeetCode 907](https://leetcode.com/problems/sum-of-subarray-minimums/)

## 代码

```java
class Solution {
    public int sumSubarrayMins(int[] arr) {
        int MOD = 1_000_000_007;
        int n = arr.length;
        int[] left = new int[n];
        int[] right = new int[n];
        Stack<Integer> stack = new Stack<>();

        // 找左边界：左边第一个严格小于 arr[i] 的位置
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && arr[stack.peek()] >= arr[i]) {
                stack.pop();
            }
            left[i] = stack.isEmpty() ? -1 : stack.peek();
            stack.push(i);
        }

        stack.clear();

        // 找右边界：右边第一个小于等于 arr[i] 的位置
        for (int i = n - 1; i >= 0; i--) {
            while (!stack.isEmpty() && arr[stack.peek()] > arr[i]) {
                stack.pop();
            }
            right[i] = stack.isEmpty() ? n : stack.peek();
            stack.push(i);
        }

        long res = 0;
        for (int i = 0; i < n; i++) {
            long leftCount = i - left[i];
            long rightCount = right[i] - i;
            long contribution = leftCount * rightCount % MOD * arr[i] % MOD;
            res = (res + contribution) % MOD;
        }
        return (int) res;
    }
}
```

## 解题心得

- **转换思路**：不要想"每个子数组的最小值是谁"，而是想"每个元素作为最小值，能管多少个子数组"
- 用**单调栈**对每个元素找左右边界：
  - `left[i]`：左边第一个比 `arr[i]` 小的位置
  - `right[i]`：右边第一个比 `arr[i]` 小的位置
- 子数组个数 = `(i - left[i]) × (right[i] - i)`，贡献 = 个数 × `arr[i]`
- **处理重复元素**：左边用 `>=`（严格小于），右边用 `>`（小于等于），保证每个子数组只被一个元素认领，不会重复计算

## 易错点

- **不能用回溯暴力枚举**：O(n²) 超时，且回溯容易生成非连续子数组导致答案错误
- **leftCount 和 rightCount 必须用 long**：两个 int 相乘可能溢出，导致 Wrong Answer
- **取模要注意**：每步乘法后都要 `% MOD`，防止中间结果溢出

## 犯过的错

1. 一开始用回溯枚举所有子数组，思路不对且超时
2. `leftCount` 和 `rightCount` 用了 int，大数据溢出导致 WA
