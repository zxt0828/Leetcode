# LC 560. Subarray Sum Equals K — 前缀和 + HashMap

## 核心思路

用前缀和把"子数组的和"转化成"两个前缀和的差"。

```
[___________大区间 sum___________]
[__小区间 sum-k__][___差值 = k___]
                  ↑             ↑
              某个旧位置      当前位置
```

大区间 `sum` 减去小区间 `sum - k` 等于 k，中间那段就是和为 k 的子数组。

## 关键公式

```
sum[0..j] - sum[0..i-1] = k
→ sum[0..i-1] = sum[0..j] - k
→ 遍历到 j 时，查 map 里有没有 sum - k
```

## 代码

```java
public int subarraySum(int[] nums, int k) {
    HashMap<Integer, Integer> map = new HashMap<>();
    int count = 0;
    int sum = 0;
    map.put(0, 1);  // 前缀和为0出现1次

    for (int i = 0; i < nums.length; i++) {
        sum += nums[i];
        if (map.containsKey(sum - k)) {
            count += map.get(sum - k);
        }
        map.put(sum, map.getOrDefault(sum, 0) + 1);
    }

    return count;
}
```

## 逐行解释

| 代码 | 作用 |
|---|---|
| `map.put(0, 1)` | 前缀和为 0 出现 1 次，处理从 index 0 开始的子数组 |
| `sum += nums[i]` | 累加前缀和 |
| `map.containsKey(sum - k)` | 查之前有没有前缀和等于 sum-k 的位置 |
| `count += map.get(sum - k)` | 出现几次就有几个和为 k 的子数组 |
| `map.put(sum, ...)` | 记录当前前缀和出现次数 |

## 模拟：nums = [1, 2, 3], k = 3

```
初始: map = {0: 1}, sum = 0, count = 0

i=0, num=1:
  sum = 1
  sum - k = 1 - 3 = -2 → map 里没有
  map = {0:1, 1:1}, count = 0

i=1, num=2:
  sum = 3
  sum - k = 3 - 3 = 0 → map 里有, 出现 1 次
  count = 1 → 对应子数组 [1, 2]
  map = {0:1, 1:1, 3:1}

i=2, num=3:
  sum = 6
  sum - k = 6 - 3 = 3 → map 里有, 出现 1 次
  count = 2 → 对应子数组 [3]
  map = {0:1, 1:1, 3:1, 6:1}

返回 2
```

## 为什么 map.put(0, 1) 必不可少？

没有它的话，当前缀和恰好等于 k 时（如 sum=3, k=3），sum - k = 0，map 里找不到 0，就漏掉了从头开始的子数组。

## 为什么不能用滑动窗口？

数组里有负数。滑动窗口依赖"右移 right 和变大，右移 left 和变小"的单调性。有负数时加一个元素可能让和变小，窗口缩放方向不确定，滑动窗口失效。

## 易错点

- HashMap 的 key 是前缀和的值，value 是出现次数（不是位置）
- 必须先查 map 再 put 当前 sum，顺序不能反（否则自己匹配自己）
- 这道题求的是数量不是具体子数组，所以只存次数不存位置
