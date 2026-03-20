# 767. Reorganize String

**难度:** Medium

**标签:** Heap

## 题目链接

[LeetCode 767](https://leetcode.com/problems/reorganize-string/)

## 代码

```java
class Solution {
    public String reorganizeString(String s) {
        int[] freq = new int[26];
        for (char c : s.toCharArray()) {
            freq[c - 'a']++;
        }

        // 如果某个字符频率超过一半，不可能重排
        for (int i = 0; i < 26; i++) {
            if (freq[i] > (s.length() + 1) / 2) {
                return "";
            }
        }

        // 最大堆，按频率降序
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) -> b[1] - a[1]);
        for (int i = 0; i < 26; i++) {
            if (freq[i] > 0) {
                maxHeap.offer(new int[]{i, freq[i]});
            }
        }

        StringBuilder sb = new StringBuilder();
        while (maxHeap.size() >= 2) {
            int[] first = maxHeap.poll();
            int[] second = maxHeap.poll();

            sb.append((char)(first[0] + 'a'));
            sb.append((char)(second[0] + 'a'));

            first[1]--;
            second[1]--;

            if (first[1] > 0) maxHeap.offer(first);
            if (second[1] > 0) maxHeap.offer(second);
        }

        if (maxHeap.size() == 1) {
            sb.append((char)(maxHeap.poll()[0] + 'a'));
        }

        return sb.toString();
    }
}
```

## 解题心得

- **贪心思想**：每次取频率最高的两个字符交替放置，保证相邻不同
- 堆会自动帮你选当前频率最高的，每轮取两个各放一个，频率差不会越拉越大
- 判断不可能的条件：某个字符频率超过 `(n+1)/2` 就一定会相邻重复

### 为什么每次取两个最高频的就能保证正确？

- 每轮两个字符都减 1，堆自动重新排序
- 只要没有字符频率超过一半，堆里总能凑出两个不同的字符来交替放

## 易错点

### 1. 双指针交换思路行不通
一开始用 slow/fast 双指针，相邻相同就交换，会导致死循环（比如 `"aab"` 不断交换但永远相邻相同）

### 2. PriorityQueue 的 comparator 参数顺序搞反
```java
// 错误：参数名反了，变成最小堆
PriorityQueue<int[]> maxHeap = new PriorityQueue<>((b, a) -> (b[1] - a[1]));

// 正确：b[1] - a[1] = 第二个减第一个 = 降序 = 最大堆
PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) -> b[1] - a[1]);
```
**记住**：`(a, b) -> a - b` 是升序（最小堆），`(a, b) -> b - a` 是降序（最大堆）。参数名别乱换，容易搞混。

### 3. comparator 里数组下标写错
```java
// 错误：b[2] 越界，数组只有 index 0 和 1
(b, a) -> (b[1] - b[2])

// 正确：比较的是两个数组的 [1] 位置
(a, b) -> b[1] - a[1]
```

## 犯过的错

1. 用双指针交换 → 死循环 TLE
2. comparator 参数名写成 `(b, a)` 导致排序方向反了
3. comparator 里 `b[2]` 下标越界 → ArrayIndexOutOfBoundsException
