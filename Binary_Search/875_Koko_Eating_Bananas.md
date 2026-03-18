# 875. Koko Eating Bananas

**刷题日期:** 2026-03-18

**难度:** Medium

**标签:** Binary Search

## 题目截图

<!-- 可以截图放到这里，或者贴题目链接 -->
[LeetCode 链接](https://leetcode.com/problems/koko-eating-bananas/)

## 代码

```java
class Solution {
    public int minEatingSpeed(int[] piles, int h) {
        int r = 0, res = Integer.MAX_VALUE, l = 1;
        for (int p : piles) r = Math.max(r, p);

        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (canEat(piles, h, mid)) {
                r = mid - 1;
                res = Math.min(res, mid);
            } else {
                l = mid + 1;
            }
        }
        return res;
    }

    private boolean canEat(int[] piles, int h, int speed) {
        long hour = 0;
        for (int i = 0; i < piles.length; i++) {
            hour += (int) Math.ceil(piles[i] * 1.0 / speed);
        }
        return hour <= h;
    }
}
```

## 解题心得

- 一开始的 `l` 要从 1 开始走，因为 `0/x` 是 0
- `hour` 变量要用 `long` 接住，防止溢出
- 计算每堆香蕉需要的时间时，要先用 `double` 计算（`piles[i] * 1.0 / speed`），因为 `int` 自动向下取整，然后再转成 `int`

## 错误原因 / 易错点

<!-- 记录你为什么做错，下次要注意什么 -->

