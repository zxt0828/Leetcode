# 678. Valid Parenthesis String

**难度:** Medium

**标签:** Stack

## 题目链接

[LeetCode 678](https://leetcode.com/problems/valid-parenthesis-string/)

## 代码

```java
class Solution {
    public boolean checkValidString(String s) {
        Stack<Integer> leftStack = new Stack<>();  // 存 '(' 的下标
        Stack<Integer> starStack = new Stack<>();  // 存 '*' 的下标

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '(') {
                leftStack.push(i);
            } else if (c == '*') {
                starStack.push(i);
            } else {
                // 遇到 ')'，优先用 '(' 匹配，没有就用 '*'
                if (!leftStack.isEmpty()) {
                    leftStack.pop();
                } else if (!starStack.isEmpty()) {
                    starStack.pop();
                } else {
                    return false;
                }
            }
        }

        // 剩下的 '(' 必须被右边的 '*' 匹配
        while (!leftStack.isEmpty() && !starStack.isEmpty()) {
            if (leftStack.pop() > starStack.pop()) {
                return false;  // '(' 在 '*' 右边，无法匹配
            }
        }

        return leftStack.isEmpty();
    }
}
```

## 解题心得

- 不能用一个普通栈解决，因为 `*` 有三种可能（`(`、`)`、空），一个栈处理不了这种不确定性
- 用**两个栈**：`leftStack` 存 `(` 的下标，`starStack` 存 `*` 的下标
- 遇到 `)` 时，优先消耗 `(`，没有 `(` 了再用 `*` 当 `(` 匹配
- 遍历结束后，剩余的 `(` 必须被它**右边**的 `*` 消掉（`*` 当 `)` 用），所以要比较下标位置
- 存下标而不是存字符，是因为最后需要判断 `*` 是否在 `(` 的右边

## 易错点

- **char 是基本类型，不能用 `.equals()`**，要用 `==` 比较
- **`||` 和 `&&` 搞混**：判断"栈顶既不是 `*` 也不是 `(`"应该用 `&&`，用 `||` 的话条件永远为 true
- **不要在方法开头加特判后又写后续逻辑**：if/else 里两个分支都 return 了，后面的代码变成 unreachable，编译报错
- **单栈思路行不通**：`*` 的多义性导致单栈无法确定该怎么处理，必须分开存储再最后统一匹配

## 我犯过的错

1. 用 `'('.equals(c)` 比较 char，编译报错 "char cannot be dereferenced"
2. 用 `||` 代替 `&&` 判断栈顶，逻辑永远为 true
3. 加了一个提前 return 的特判，导致后面代码 unreachable
4. 一开始想用单栈解决，遇到 `)` 时直接 pop 判断，无法处理 `*` 的多种角色
