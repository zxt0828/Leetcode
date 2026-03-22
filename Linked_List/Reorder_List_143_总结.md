# LeetCode 143 - Reorder List 总结

## 题目描述

给定链表 `L: L0 → L1 → … → Ln-1 → Ln`，将其重新排列为：

```
L0 → Ln → L1 → Ln-1 → L2 → Ln-2 → …
```

**不能修改节点的值**，只能通过节点重排来完成。

**示例：**

```
输入: [1, 2, 3, 4]      →  输出: [1, 4, 2, 3]
输入: [1, 2, 3, 4, 5]   →  输出: [1, 5, 2, 4, 3]
```

---

## 解题思路（三步法）

整体思路：把链表从中间断开，反转后半段，然后交替合并两段。

```
原始:      1 → 2 → 3 → 4 → 5

Step 1 找中点:  [1 → 2 → 3]   [4 → 5]
Step 2 反转:    [1 → 2 → 3]   [5 → 4]
Step 3 合并:    1 → 5 → 2 → 4 → 3
```

---

## 知识点一：快慢指针找中点

### 核心思想

用两个指针同时出发，`slow` 每次走一步，`fast` 每次走两步。当 `fast` 到达末尾时，`slow` 刚好在中间位置。

### 代码模板

```java
ListNode slow = head, fast = head.next;
while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
}
// slow 现在指向前半段的最后一个节点
```

### 关键细节

| 要点 | 说明 |
|------|------|
| `fast` 初始化为 `head.next` | 保证偶数长度时 `slow` 停在前半段最后一个节点，而不是后半段第一个 |
| 循环条件必须是 `fast != null && fast.next != null` | 因为 `fast` 每次走两步，必须同时检查，否则 `fast.next.next` 会空指针 |
| 奇数长度时中间节点归前半段 | 例如 `[1,2,3,4,5]`，前半段 `[1,2,3]`，后半段 `[4,5]` |

### 图示（偶数长度）

```
初始:   1 → 2 → 3 → 4
        s   f

第1轮:  1 → 2 → 3 → 4
             s       f

fast.next == null，停止。slow = 2，即前半段最后节点。
前半段: [1 → 2]    后半段: [3 → 4]
```

### 图示（奇数长度）

```
初始:   1 → 2 → 3 → 4 → 5
        s   f

第1轮:  1 → 2 → 3 → 4 → 5
             s       f

第2轮:  1 → 2 → 3 → 4 → 5
                  s       f

fast.next == null，停止。slow = 3。
前半段: [1 → 2 → 3]    后半段: [4 → 5]
```

### 常见变体

```java
// 变体：fast 从 head 开始（slow 会偏右一个位置）
ListNode slow = head, fast = head;
while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
}
// 偶数时 slow 指向后半段第一个节点
// 奇数时 slow 指向正中间节点
```

> **选哪种取决于你需要 slow 停在中点左边还是中点上。** 本题需要从中间断开，所以用 `fast = head.next` 更方便，`slow` 直接就是前半段的尾巴。

---

## 知识点二：反转链表

### 核心思想

用三个指针 `prev`、`curr`、`temp` 逐个翻转指针方向。每一步把 `curr.next` 指向 `prev`，然后整体前进一步。

### 代码模板

```java
ListNode prev = null;
ListNode curr = head; // 这里是后半段的头
while (curr != null) {
    ListNode temp = curr.next;  // 1. 先保存下一个
    curr.next = prev;           // 2. 反转指向
    prev = curr;                // 3. prev 前进
    curr = temp;                // 4. curr 前进
}
// 循环结束后，prev 是反转后链表的头节点
```

### 逐步图示

```
反转 3 → 4 → null:

初始:   prev=null  curr=3  →  4  →  null

第1轮:  temp=4
        null ← 3      4 → null
        prev=3  curr=4

第2轮:  temp=null
        null ← 3 ← 4
        prev=4  curr=null

结束！prev=4，即新的头。链表变为 4 → 3 → null
```

### 记忆口诀

> **存、转、进、进**：存下一个，转方向，prev 进，curr 进。

### 易错点

| 错误 | 后果 |
|------|------|
| 忘记 `temp = curr.next` | `curr.next` 被覆盖后找不到下一个节点，链表断裂 |
| `prev` 没初始化为 `null` | 反转后的尾部不会指向 `null`，导致环形链表 |
| 返回 `curr` 而不是 `prev` | 循环结束时 `curr == null`，`prev` 才是新头 |

---

## 知识点三：交替合并两个链表

### 核心思想

给定两个链表 `first` 和 `second`，将它们交替穿插合并：`first[0] → second[0] → first[1] → second[1] → ...`

### 代码模板

```java
ListNode first = head;       // 前半段
ListNode second = prev;      // 反转后的后半段
while (first != null && second != null) {
    ListNode tmp1 = first.next;   // 保存 first 的下一个
    ListNode tmp2 = second.next;  // 保存 second 的下一个

    first.next = second;          // first 指向 second
    second.next = tmp1;           // second 指向 first 的原下一个

    first = tmp1;                 // 前进
    second = tmp2;                // 前进
}
```

### 逐步图示

```
合并 [1 → 2 → 3] 和 [5 → 4]:

第1轮: first=1, second=5
       tmp1=2, tmp2=4
       1 → 5 → 2    (first.next=5, 5.next=2)
       first=2, second=4

第2轮: first=2, second=4
       tmp1=3, tmp2=null
       2 → 4 → 3    (first.next=4, 4.next=3)
       first=3, second=null

second == null，循环结束。
最终: 1 → 5 → 2 → 4 → 3
```

### 为什么循环条件是 `&&` 而不是 `||`

后半段长度 ≤ 前半段长度（最多少一个节点），所以 `second` 一定先结束或同时结束。用 `&&` 保证不会对 `null` 操作。前半段多出的尾节点自然留在末尾，不需要额外处理。

---

## 完整代码

```java
class Solution {
    public void reorderList(ListNode head) {
        if (head == null || head.next == null) return;

        // Step 1: 快慢指针找中点
        ListNode slow = head, fast = head.next;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }

        // Step 2: 反转后半段
        ListNode second = slow.next;
        slow.next = null;          // 断开前后两段
        ListNode prev = null;
        while (second != null) {
            ListNode temp = second.next;
            second.next = prev;
            prev = second;
            second = temp;
        }

        // Step 3: 交替合并
        ListNode first = head;
        while (first != null && prev != null) {
            ListNode tmp1 = first.next;
            ListNode tmp2 = prev.next;
            first.next = prev;
            prev.next = tmp1;
            first = tmp1;
            prev = tmp2;
        }
    }
}
```

**时间复杂度：** O(n) — 每个节点最多被访问常数次

**空间复杂度：** O(1) — 只用了几个指针变量，原地操作

---

## 面试表达建议

> "这道题可以分三步：第一步用快慢指针找到链表中点，把链表分成前后两半；第二步把后半段原地反转；第三步把前半段和反转后的后半段交替合并。三步都是 O(n) 时间、O(1) 空间，整体也是。"

---

## 相关题目

| 题号 | 题目 | 关联知识点 |
|------|------|-----------|
| 876 | Middle of the Linked List | 快慢指针找中点 |
| 206 | Reverse Linked List | 反转链表 |
| 21 | Merge Two Sorted Lists | 合并链表 |
| 234 | Palindrome Linked List | 找中点 + 反转 + 比较 |
| 148 | Sort List | 找中点 + 归并排序 |
