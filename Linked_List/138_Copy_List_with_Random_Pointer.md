# 138. Copy List with Random Pointer

- **难度**: Medium
- **分类**: Linked List / Hash Table
- **链接**: https://leetcode.com/problems/copy-list-with-random-pointer/

---

## 题目描述

给定一个链表，每个节点有 `next` 和 `random` 两个指针。`random` 可以指向链表中任意节点或 null。要求创建该链表的**深拷贝**，新链表中的所有指针都不能指向原链表的节点。

---

## 核心难点

拷贝 `next` 很简单，难的是 `random`：当你拷贝节点 A 时，`A.random` 指向的节点 C 的拷贝可能还没创建，或者已经创建了但你找不到。核心问题是：**怎么从原节点快速找到对应的拷贝节点？**

---

## 解法一：HashMap（推荐先掌握）

用 `HashMap<原节点, 拷贝节点>` 存映射关系。

### 思路

1. **第一轮遍历**：为每个原节点创建拷贝节点，存入 map
2. **第二轮遍历**：通过 map 查找，设置每个拷贝节点的 `next` 和 `random`

### 代码

```java
class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) return null;

        Map<Node, Node> map = new HashMap<>();

        // 第一轮：创建所有拷贝节点
        Node cur = head;
        while (cur != null) {
            map.put(cur, new Node(cur.val));
            cur = cur.next;
        }

        // 第二轮：连接 next 和 random
        cur = head;
        while (cur != null) {
            map.get(cur).next = map.get(cur.next);
            map.get(cur).random = map.get(cur.random);
            cur = cur.next;
        }

        return map.get(head);
    }
}
```

### 复杂度

| | 复杂度 | 说明 |
|---|---|---|
| 时间 | O(n) | 两次遍历 |
| 空间 | O(n) | HashMap 存 n 个映射 |

---

## 解法二：原地交织（O(1) 空间优化）

不用 HashMap，把拷贝节点直接插在原节点后面，利用位置关系来找对应的拷贝。

### 思路

**Step 1 — 交织插入**：在每个原节点后面插入拷贝节点

```
A → B → C
变成
A → A' → B → B' → C → C'
```

**Step 2 — 设置 random**：`A'.random = A.random.next`，因为原节点 random 的下一个就是对应拷贝

**Step 3 — 拆分**：把交织链表拆成原链表和拷贝链表两条独立链表

### 代码

```java
class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) return null;

        // Step 1: 交织插入 A → A' → B → B' → C → C'
        Node cur = head;
        while (cur != null) {
            Node copy = new Node(cur.val);
            copy.next = cur.next;
            cur.next = copy;
            cur = copy.next;
        }

        // Step 2: 设置 random
        cur = head;
        while (cur != null) {
            if (cur.random != null) {
                cur.next.random = cur.random.next;
            }
            cur = cur.next.next;
        }

        // Step 3: 拆分两条链表
        Node newHead = head.next;
        cur = head;
        while (cur != null) {
            Node copy = cur.next;
            cur.next = copy.next;
            copy.next = (copy.next != null) ? copy.next.next : null;
            cur = cur.next;
        }

        return newHead;
    }
}
```

### 复杂度

| | 复杂度 | 说明 |
|---|---|---|
| 时间 | O(n) | 三次遍历 |
| 空间 | O(1) | 不需要额外数据结构 |

---

## 两种解法对比

| | HashMap | 原地交织 |
|---|---|---|
| 核心思想 | 用 map 存原节点→拷贝节点的映射 | 把拷贝插在原节点后面，用位置关系代替 map |
| 空间 | O(n) | O(1) |
| 代码难度 | 简单直观 | 拆分步骤容易出错 |
| 面试建议 | 先写这个 | 被追问优化空间时再写 |

---

## 易错点 & 复习提醒

- **HashMap 解法中 `map.get(cur.next)` 和 `map.get(cur.random)` 不需要判空**：HashMap 的 `get(null)` 返回 null，刚好符合链表末尾和 random 为 null 的情况。
- **原地交织法 Step 2 中 `cur.random != null` 必须判空**：如果原节点 random 是 null，`cur.random.next` 会 NPE。
- **原地交织法 Step 3 拆分时必须恢复原链表**：面试官可能要求不能修改原链表结构，拆分步骤要同时恢复 `cur.next`。
- **别忘了 `head == null` 的边界检查**。

---

## 相关题目

- [133. Clone Graph](https://leetcode.com/problems/clone-graph/) — 图的深拷贝，同样用 HashMap 解决
- [1485. Clone Binary Tree With Random Pointer](https://leetcode.com/problems/clone-binary-tree-with-random-pointer/) — 二叉树版本的深拷贝
