# 394. Decode String

- **难度**: Medium
- **分类**: Stack / String
- **链接**: https://leetcode.com/problems/decode-string/

---

## 题目描述

给定一个编码字符串，规则是 `k[encoded_string]`，表示括号内的字符串重复 k 次。可以嵌套，如 `3[a2[c]]` = `accaccacc`。返回解码后的字符串。

---

## 核心思路：双栈

遇到嵌套结构就想到栈。用两个栈分工：

- `strStack`：保存遇到 `[` 之前已经拼好的字符串（上下文）
- `numStack`：保存 `[` 前面的重复次数

### 四种字符的处理

| 字符类型 | 操作 |
|---|---|
| 数字 | 累积计算 `k = k * 10 + digit`（处理多位数如 `12[a]`） |
| `[` | 保存现场：把 currentStr 和 k 分别压入两个栈，然后重置 |
| 字母 | 直接追加到 currentStr |
| `]` | 恢复现场：pop 出之前的字符串 prev 和重复次数 repeat，把 currentStr 重复 repeat 次拼到 prev 后面 |

### 核心逻辑：遇到 `]` 时的拼接

```java
String prev = strStack.pop();          // 括号前的内容
int repeat = numStack.pop();           // 重复几次
StringBuilder tmp = new StringBuilder(prev);  // 从 prev 开始（不是空字符串！）
for (int i = 0; i < repeat; i++) {
    tmp.append(current);               // 把括号里的内容重复拼上去
}
current = tmp;                         // 更新 current
```

关键点：`tmp` 从 `prev` 开始，不是从空字符串开始，这样才能把括号前的内容和括号里重复的内容拼在一起。

---

## 手动模拟：`3[a2[c]]`

```
'3'  → k = 3
'['  → strStack push ""，numStack push 3，reset current=""，k=0
'a'  → current = "a"
'2'  → k = 2
'['  → strStack push "a"，numStack push 2，reset current=""，k=0
'c'  → current = "c"

']'  → pop: prev="a", repeat=2
       tmp = "a" + "c"×2 = "acc"
       current = "acc"

']'  → pop: prev="", repeat=3
       tmp = "" + "acc"×3 = "accaccacc"
       current = "accaccacc"

答案："accaccacc"
```

## 手动模拟：`xy2[a3[b]]z`

```
'x'  → current = "x"
'y'  → current = "xy"
'2'  → k = 2
'['  → strStack push "xy"，numStack push 2，reset current=""，k=0
'a'  → current = "a"
'3'  → k = 3
'['  → strStack push "a"，numStack push 3，reset current=""，k=0
'b'  → current = "b"

']'  → pop: prev="a", repeat=3
       tmp = "a" + "b"×3 = "abbb"
       current = "abbb"

']'  → pop: prev="xy", repeat=2
       tmp = "xy" + "abbb"×2 = "xyabbbabbb"
       current = "xyabbbabbb"

'z'  → current = "xyabbbabbbz"

答案："xyabbbabbbz"
```

---

## 代码

```java
class Solution {
    public String decodeString(String s) {
        Stack<String> strStack = new Stack<>();
        Stack<Integer> numStack = new Stack<>();
        StringBuilder current = new StringBuilder();
        int k = 0;

        for (char c : s.toCharArray()) {
            if (Character.isDigit(c)) {
                k = k * 10 + (c - '0');
            } else if (c == '[') {
                // 保存现场
                strStack.push(current.toString());
                numStack.push(k);
                current = new StringBuilder();
                k = 0;
            } else if (c == ']') {
                // 恢复现场 + 拼接
                String prev = strStack.pop();
                int repeat = numStack.pop();
                StringBuilder tmp = new StringBuilder(prev);
                for (int i = 0; i < repeat; i++) {
                    tmp.append(current);
                }
                current = tmp;
            } else {
                current.append(c);
            }
        }

        return current.toString();
    }
}
```

---

## 复杂度分析

| | 复杂度 | 说明 |
|---|---|---|
| 时间 | O(输出字符串长度) | 每个输出字符都被拼接一次 |
| 空间 | O(嵌套深度) | 栈的深度取决于括号的嵌套层数 |

---

## 易错点 & 复习提醒

- **多位数的处理**：`k = k * 10 + digit`，不能直接 `k = digit`，否则 `12[a]` 会被当成 `2[a]`。
- **`[` 时必须重置 currentStr 和 k**：不重置的话，括号外的内容会混进括号里。
- **`]` 时 tmp 从 prev 开始，不是从空字符串开始**：`new StringBuilder(prev)` 不是 `new StringBuilder()`，否则括号前面的字母会丢失。
- **两个栈同进同出**：遇到 `[` 同时 push，遇到 `]` 同时 pop，始终保持同步。
- **StringBuilder 可以直接 append 另一个 StringBuilder**：不需要先 `toString()`。

---

## 相关题目

- [726. Number of Atoms](https://leetcode.com/problems/number-of-atoms/) — 类似的括号嵌套展开，但处理的是化学式
- [1190. Reverse Substrings Between Each Pair of Parentheses](https://leetcode.com/problems/reverse-substrings-between-each-pair-of-parentheses/) — 括号内翻转，同样用栈处理嵌套
