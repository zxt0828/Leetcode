# 239. Sliding Window Maximum

- **难度**: Hard
- **分类**: Heap / Monotonic Deque / Sliding Window
- **链接**: https://leetcode.com/problems/sliding-window-maximum/

---

## 题目描述

给定一个整数数组 `nums` 和一个滑动窗口大小 `k`，窗口从左到右每次移动一位，返回每个窗口内的最大值。

---

## 解法一：最大堆 + 懒删除

### 核心难点

用最大堆维护窗口最大值时，当窗口左边界右移，堆中间的元素没法高效删除（`remove()` 是 O(n)）。

### 解决方案：懒删除（Lazy Removal）

不主动从堆中删元素，而是取堆顶时检查它的**下标**是否还在窗口内。如果不在就 poll 掉，直到堆顶在窗口范围内。

关键：堆里存 `{值, 下标}`，不能只存值，否则无法判断是否在窗口内。

### 为什么懒删除可行？

已经滑出窗口但还在堆里的元素，只要不是堆顶（最大值），就不影响结果。只有"浮"到堆顶时才需要处理，这时 poll 掉即可。

### 代码

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        int[] res = new int[n - k + 1];

        // 存 {值, 下标}，按值降序
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) -> b[0] - a[0]);

        for (int i = 0; i < n; i++) {
            maxHeap.offer(new int[]{nums[i], i});

            // 窗口还没满，跳过
            if (i < k - 1) continue;

            // 懒删除：堆顶下标不在窗口 [i-k+1, i] 内就扔掉
            while (maxHeap.peek()[1] < i - k + 1) {
                maxHeap.poll();
            }

            res[i - k + 1] = maxHeap.peek()[0];
        }

        return res;
    }
}
```

### 复杂度

| | 复杂度 | 说明 |
|---|---|---|
| 时间 | O(n log n) | 每个元素最多入堆出堆各一次，每次 O(log n) |
| 空间 | O(n) | 堆中最多存 n 个元素（懒删除不立即移除） |

---

## 解法二：单调递减双端队列（最优解）

### 核心思想

维护一个**递减的双端队列（Deque）**，队列里存下标。队头永远是当前窗口的最大值。

### 两个维护规则

1. **入队前清尾**：新元素进来前，把队尾所有比它小的元素都移除（它们永远不可能成为最大值了）
2. **检查队头过期**：如果队头下标已经滑出窗口范围，移除队头

### 代码

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        int[] res = new int[n - k + 1];
        Deque<Integer> deque = new ArrayDeque<>(); // 存下标

        for (int i = 0; i < n; i++) {
            // 1. 清尾：移除所有比 nums[i] 小的（维护递减）
            while (!deque.isEmpty() && nums[deque.peekLast()] <= nums[i]) {
                deque.pollLast();
            }

            deque.offerLast(i);

            // 2. 检查队头是否过期（滑出窗口）
            if (deque.peekFirst() < i - k + 1) {
                deque.pollFirst();
            }

            // 3. 窗口满了之后，队头就是最大值
            if (i >= k - 1) {
                res[i - k + 1] = nums[deque.peekFirst()];
            }
        }

        return res;
    }
}
```

### 复杂度

| | 复杂度 | 说明 |
|---|---|---|
| 时间 | O(n) | 每个元素最多入队出队各一次 |
| 空间 | O(k) | 队列最多存 k 个元素 |

---

## 两种解法对比

| | 最大堆 + 懒删除 | 单调双端队列 |
|---|---|---|
| 时间 | O(n log n) | O(n) |
| 空间 | O(n) | O(k) |
| 代码难度 | 较简单，堆的用法直观 | 需要理解单调队列的维护逻辑 |
| 面试建议 | 先说这个思路，展示你会用堆 | 被追问优化时给出这个，是最优解 |

---

## 堆操作时间复杂度速查

| 操作 | 时间复杂度 | 说明 |
|---|---|---|
| `offer()` 插入 | O(log n) | sift-up |
| `peek()` 查看堆顶 | O(1) | 数组第一个元素 |
| `poll()` 取出堆顶 | O(log n) | sift-down |
| `remove(obj)` 删除指定元素 | O(n) | 线性查找 + 调整 |
| `size()` / `isEmpty()` | O(1) | 维护 count 变量 |

`remove()` 是 O(n) 这一点是这道题用懒删除而不是直接删除的根本原因。

---

## 易错点 & 复习提醒

- **堆里必须存下标**：只存值无法判断元素是否在窗口内，懒删除就没法做。
- **懒删除用 while 不是 if**：堆顶可能连续多个都过期了，要一直 poll 到合法为止。
- **单调队列清尾的条件是 `<=` 不是 `<`**：相等的也要移除，因为后进来的下标更大，存活时间更长，留旧的没意义。
- **结果数组大小是 `n - k + 1`**：不是 n，别开错了。
- **窗口未满时不记录结果**：前 k-1 个元素只入队，不写入 res。

---

## 相关题目

- [76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/) — 滑动窗口经典
- [155. Min Stack](https://leetcode.com/problems/min-stack/) — 类似"维护极值"的思想
- [862. Shortest Subarray with Sum at Least K](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/) — 单调双端队列的进阶应用
