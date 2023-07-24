# 前缀和数组

前缀和主要用于快速的计算一个数组中一个范围内的数值之和。

我们来看[leetcode303](https://leetcode.com/problems/range-sum-query-immutable/description/)，这题要我们计算数组区间内的和，这是非常标准的前缀和问题。

当然我们第一直觉是这样写：

```java
class NumArray {

    private int[] nums;

    public NumArray(int[] nums) {
        this.nums = nums;
    }
    
    public int sumRange(int left, int right) {
        int res = 0;
        for (int i = left; i <= right; i++) {
            res += nums[i];
        }
        return res;
    }
}

```

这样的效率是很差的，每次调用sumRange方法的时候，都要去扫描一遍数组，时间复杂度是O(N)，其中N是数组的长度。那么如何用前缀和实现呢？

```java
class NumArray {
    // 前缀和数组
    int[] preSum;

    public NumArray(int[] nums) {
        int n = nums.length;
        // preSum[0] = 0，便于计算累加和
        preSum = new int[n + 1];
        for (int i = 0; i < nums.length; i++) {
            preSum[i + 1] = preSum[i] + nums[i];
        }
    }

    /* 查询闭区间 [left, right] 的累加和 */
    public int sumRange(int left, int right) {
        return preSum[right + 1] - preSum[left];
    }
}
```

核心思路是我们 new 一个新的数组 `preSum` 出来，`preSum[i]` 记录 `nums[0..i-1]` 的累加和，看图 10 = 3 + 5 + 2：

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

这样一来，如果我们想要求区间\[1 ,4]之间的数值和，就可以通过`preSum[5] - preSum[1]`来得到。这样每次调用sumRange函数时，只需要进行一次运算即可，时间复杂度为O(1)。

同样的，在二维数组中，一样可以使用前缀和的思路，比如[leetcode304](https://leetcode.com/problems/range-sum-query-2d-immutable/)。

这题要我们求二维矩阵中子矩阵的元素之和。

当然我们可以循环去遍历这个矩阵来求和，但是这样效率太差了，注意任意子矩阵的元素和可以转化成它周边几个大矩阵的元素和的运算：

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

那么做这道题更好的思路和一维数组中的前缀和是非常类似的，我们可以维护一个二维 `preSum` 数组，专门记录以原点为顶点的矩阵的元素之和，就可以用几次加减运算算出任何一个子矩阵的元素和：

```java
class NumMatrix {
    // 定义：preSum[i][j] 记录 matrix 中子矩阵 [0, 0, i-1, j-1] 的元素和
    int[][] preSum;

    public NumMatrix(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        preSum = new int[m + 1][n + 1];
        for (int i = 1; i < m + 1; i++) {
            for (int j = 1; j < n + 1; j++) {
            // 计算每个矩阵 [0, 0, i, j] 的元素和
                preSum[i][j] = preSum[i - 1][j] + preSum[i][j - 1] - preSum[i - 1][j - 1] + matrix[i - 1][j - 1];
            }
        }
    }
    
    // 计算子矩阵 [x1, y1, x2, y2] 的元素和
    public int sumRegion(int row1, int col1, int row2, int col2) {
        return preSum[row2 + 1][col2 + 1] - preSum[row1][col2 + 1] - preSum[row2 + 1][col1] + preSum[row1][col1];
    }
}
```

这样，`sumRegion` 函数的时间复杂度也用前缀和技巧优化到了 O(1)。
