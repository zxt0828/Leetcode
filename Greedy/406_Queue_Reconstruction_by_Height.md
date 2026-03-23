# 406. Queue Reconstruction by Height

**难度:** Medium

**标签:** Greedy

## 题目链接

[LeetCode 406](https://leetcode.com/problems/queue-reconstruction-by-height/)

## 代码

```java
class Solution {
    public int[][] reconstructQueue(int[][] people) {
        // 按 h 降序，h 相同按 k 升序
        Arrays.sort(people, (a, b) -> {
            if (a[0] != b[0]) return b[0] - a[0];
            return a[1] - b[1];
        });

        List<int[]> list = new ArrayList<>();
        for (int[] p : people) {
            list.add(p[1], p);  // 插入到 index k 的位置
        }

        return list.toArray(new int[people.length][]);
    }
}
```

## 解题心得

### 贪心有时候需要在两个维度上分别贪心

这道题每个人有两个属性 `[h, k]`，不能只按一个维度排序。排序策略是：
- **h 降序**：高个子先处理，占好位置
- **h 相同时 k 升序**：同身高的人，k 小的先插入

排完序后，按 k 值插入到结果列表的第 k 个位置，高个子先占位，矮个子后插入不会影响高个子的 k 值（因为矮个子不会被高个子"看见"）。

### 关键语法：`list.add(index, element)`

在指定位置插入元素，后面的元素自动往后挪，跟 `list.add(element)`（追加到末尾）不同。

## 易错点

- 排序方向搞反：h 必须降序，k 必须升序，反了就不对
- 不能用数组直接做插入，因为需要频繁在中间插入元素，ArrayList 的 `add(index, element)` 更方便
- `list.toArray(new int[people.length][])` 是把 List 转回二维数组的固定写法
