# 差分数组

## **算法原理**

**差分数组的主要适用场景是频繁对原始数组的某个区间的元素进行增减**。

比如说，我给你输入一个数组 `nums`，然后又要求给区间 `nums[2..6]` 全部加 1，再给 `nums[3..9]` 全部减 3，再给 `nums[0..4]` 全部加 2，再给...

常规的思路很容易，你让我给区间 `nums[i..j]` 加上 `val`，那我就一个 for 循环给它们都加上呗，还能咋样？这种思路的时间复杂度是 O(N)，由于这个场景下对 `nums` 的修改非常频繁，所以效率会很低下。

这时我们就需要差分数组，对数组构造一个 `diff` 差分数组，**`diff[i]` 就是 `nums[i]` 和 `nums[i-1]` 之差**

```java
int[] diff = new int[nums.length];
// 构造差分数组
diff[0] = nums[0];
for (int i = 1; i < nums.length; i++) {
    diff[i] = nums[i] - nums[i - 1];
}
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

用这个差分数组可以推导出原数组的值，如下

```java
int[] res = new int[diff.length];
// 根据差分数组构造结果数组
res[0] = diff[0];
for (int i = 1; i < diff.length; i++) {
    res[i] = res[i - 1] + diff[i];
}
```

我们思考一下这个过程，`diff[i] += 3`也就意味着从i到数组最后的元素全都加了3，`diff[j] -= 3`意味着从j到数组最后的元素全都减了3。只需要用O(1)的时间，就对数组的整个区间做了修改，多次修改差分数组并且反推，可以得到原数组修改之后的值。

现在我们把差分数组写成一个类，包括增加和反推的方法。

```java
// 差分数组工具类
class Difference {
    // 差分数组
    private int[] diff;
    
    /* 输入一个初始数组，区间操作将在这个数组上进行 */
    public Difference(int[] nums) {
        assert nums.length > 0;
        diff = new int[nums.length];
        // 根据初始数组构造差分数组
        diff[0] = nums[0];
        for (int i = 1; i < nums.length; i++) {
            diff[i] = nums[i] - nums[i - 1];
        }
    }

    /* 给闭区间 [i, j] 增加 val（可以是负数）*/
    public void increment(int i, int j, int val) {
        diff[i] += val;
        if (j + 1 < diff.length) {
            diff[j + 1] -= val;
        }
    }

    /* 返回结果数组 */
    public int[] result() {
        int[] res = new int[diff.length];
        // 根据差分数组构造结果数组
        res[0] = diff[0];
        for (int i = 1; i < diff.length; i++) {
            res[i] = res[i - 1] + diff[i];
        }
        return res;
    }
}
```

## 相关题目

[leetcode370](https://leetcode.com/problems/range-addition/description/)

这题是标准的差分数组解题。由于数组初始化是全是0的数组，相较于上面的代码，可以简化一部分。

```java
class Solution {
    public int[] getModifiedArray(int length, int[][] updates) {
        // 构建差分数组
        int[] diff = new int[length];
        for (int[] update : updates) {
            int start = update[0], end = update[1], val = update[2];
            diff[start] += val;
            if (end < length - 1) diff[end + 1] -= val;
        }
        // 反推结果数组
        int[] res = new int[length];
        res[0] = diff[0];
        for (int i = 1; i < length; i++) {
            res[i] = res[i - 1] + diff[i];
        }
        return res;
    }
}
```

当然，也有一些题目不是一眼就能看出来是差分数组的，比如说[leetcode1109](https://leetcode.com/problems/corporate-flight-bookings/).

这道题绕了半天，其实就是个差分数组的题目，本质上就是在数组的区间内加上seats数量，同上。当然，这里要注意的是，题目中的n是从1开始的，需要减1才能得到数组中的索引。

```java
class Solution {
    public int[] corpFlightBookings(int[][] bookings, int n) {
        int[] diff = new int[n];
        for (int[] booking : bookings) {
            int start = booking[0] - 1, end = booking[1] - 1, seats = booking[2];
            diff[start] += seats;
            if (end < n - 1) diff[end + 1] -= seats;
        }
        int[] res = new int[n];
        res[0] = diff[0];
        for (int i = 1; i < n; i++) {
            res[i] = res[i - 1] + diff[i];
        }
        return res;
    }
}
```

还有一道类似的题目，[leetcode1094](https://leetcode.com/problems/car-pooling/description/)。

你是一个开公交车的司机，公交车的最大载客量为 `capacity`，沿途要经过若干车站，给你一份乘客行程表 `int[][] trips`，其中 `trips[i] = [num, start, end]` 代表着有 `num` 个旅客要从站点 `start` 上车，到站点 `end` 下车，请你计算是否能够一次把所有旅客运送完毕（不能超过最大载客量 `capacity`）。

这里`trips[i]`可以认为是一组区间操作，乘客的上车下车就是在这个区间内进行加减操作，最后我们要查看结果数组中是否都小于最大载客量。同时，题目中给出了车站的范围是\[1, 1000]，那么这个数组的长度也就确定了，为1001，这样可以覆盖所有的车站。

```java
class Solution {
    public boolean carPooling(int[][] trips, int capacity) {
        // 最多有 1001 个车站
        int[] count = new int[1001];
        // 构造差分解法
        for (int[] trip : trips) {
            int num = trip[0], from = trip[1], to = trip[2];
            count[from] += num;
            count[to] -= num;
        }
        // 客车自始至终都不应该超载
        int seats = 0;
        for (int i : count) {
            seats += i;
            if (seats > capacity) return false;
        }
        return true;
    }
}
```
