# 283. Move Zeroes

**刷题日期:** 2026-03-18

**难度:** Easy

**标签:** Two Pointers

## 题目链接

[LeetCode 283](https://leetcode.com/problems/move-zeroes/)

## 代码

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int slow = 0;
        for (int fast = 0; fast < nums.length; fast++) {
            if (nums[fast] != 0) {
                int temp = nums[fast];
                nums[fast] = nums[slow];
                nums[slow] = temp;
                slow++;
            }
        }
    }
}
```

## 解题心得

- 要明确两个指针各自的作用：
  - `slow`：指向当前应该放置非零元素的位置（也就是保存 0 的位置）
  - `fast`：负责遍历数组，寻找下一个不为 0 的元素
- `slow` 和 `fast` 一起从 0 出发，`fast` 每轮都走，`slow` 只在发生交换时才前进
- 遇到非零元素就和 `slow` 位置交换，这样所有 0 自然被推到数组末尾

## 易错点

- 注意是 in-place 操作，不能新建数组
- 交换而不是直接赋值，否则会丢失 `slow` 位置的值
