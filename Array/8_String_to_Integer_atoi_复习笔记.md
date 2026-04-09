# 8. String to Integer (atoi)

## 题目概述

手动实现 `atoi` 函数，将字符串转换为 32 位有符号整数。按顺序处理四个步骤：去空格 → 读符号 → 读数字 → 处理溢出。

## 核心思路

**指针分阶段推进**：用一个指针 `i` 依次完成三个阶段，每个阶段各管各的，逻辑清晰不易出错。

## 代码模版

```java
class Solution {
    public int myAtoi(String s) {
        char[] chars = s.toCharArray();
        int i = 0;

        // 第一步：跳过前导空格
        while (i < chars.length && chars[i] == ' ') i++;

        // 第二步：读取正负号
        int sign = 1;
        if (i < chars.length && (chars[i] == '-' || chars[i] == '+')) {
            if (chars[i] == '-') sign = -1;
            i++;
        }

        // 第三步：逐位读取数字，同时处理溢出
        int res = 0;
        while (i < chars.length && Character.isDigit(chars[i])) {
            int digit = chars[i] - '0';

            // 溢出判断（必须在 res * 10 + digit 之前）
            if (res > Integer.MAX_VALUE / 10 ||
                (res == Integer.MAX_VALUE / 10 && digit > 7)) {
                return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
            }

            res = res * 10 + digit;
            i++;
        }

        return res * sign;
    }
}
```

## 分步图解

以输入 `"   -042"` 为例：

```
"   -042"
 ^^^        第一步：i 从 0 跳到 3，跳过空格
    ^       第二步：i = 3，读到 '-'，sign = -1，i++
     ^^^    第三步：i = 4,5,6，依次读 '0','4','2'
            res: 0 → 0 → 4 → 42
            最终返回 42 * (-1) = -42
```

## 数字构建过程

```
res = 0
读到 '1': res = 0 * 10 + 1 = 1
读到 '2': res = 1 * 10 + 2 = 12
读到 '3': res = 12 * 10 + 3 = 123
```

关键：**先乘 10，再加当前数字**。

## 溢出判断详解

`Integer.MAX_VALUE = 2147483647`，`Integer.MAX_VALUE / 10 = 214748364`

在执行 `res = res * 10 + digit` **之前**检查：

| 条件 | 说明 |
|------|------|
| `res > 214748364` | 乘 10 后至少是 2147483650，必溢出 |
| `res == 214748364 && digit > 7` | 乘 10 后是 2147483640，加上 >7 的数超过 2147483647 |

为什么是 **7**？因为 `Integer.MAX_VALUE` 的最后一位是 7。

溢出时根据符号返回对应边界值：正数返回 `Integer.MAX_VALUE`，负数返回 `Integer.MIN_VALUE`。

## 常用 API

| 方法 | 作用 |
|------|------|
| `s.toCharArray()` | 字符串转 char 数组 |
| `Character.isDigit(c)` | 判断字符是否为数字 |
| `chars[i] - '0'` | 字符数字转 int（利用 ASCII 码差值） |

## 易错点

| 易错点 | 说明 |
|--------|------|
| 没跳空格就开始读 | 遇到空格直接 break 导致后面内容全部丢失 |
| 符号位置写死 `chars[0]` | 符号前可能有空格，必须用指针定位 |
| 溢出判断放在操作之后 | `int` 一旦溢出值就已经错了，必须在 `*10 + digit` 之前检查 |
| 先加后乘 | 应该是先乘 10 再加当前 digit |
| 符号读取多次 | 符号最多读一个，用 if 而不是 while |

## 复杂度

- **时间**：O(n)，遍历一次字符串
- **空间**：O(n)，`toCharArray()` 创建了新数组（也可以用 `s.charAt(i)` 做到 O(1)）

## 相关题目

- LeetCode 7. Reverse Integer（同样需要溢出判断）
- LeetCode 65. Valid Number（更复杂的字符串解析）
- LeetCode 12/13. Roman to Integer（字符串转数字的另一种形式）
