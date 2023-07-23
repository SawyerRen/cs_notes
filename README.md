# 二分查找

本文就来探究几个最常用的二分查找场景：寻找一个数、寻找左侧边界、寻找右侧边界。而且，我们就是要深入细节，比如不等号是否应该带等号，`mid` 是否应该加一等等。分析这些细节的差异以及出现这些差异的原因，保证你能灵活准确地写出正确的二分查找算法。

## 二分查找框架

```java
int binarySearch(int[] nums, int target) {
    int left = 0, right = ...;
​
    while(...) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            ...
        } else if (nums[mid] < target) {
            left = ...
        } else if (nums[mid] > target) {
            right = ...
        }
    }
    return ...;
}
```

**分析二分查找的一个技巧是：不要出现 else，而是把所有情况用 else if 写清楚，这样可以清楚地展现所有细节**。

**另外提前说明一下，计算 `mid` 时需要防止溢出**，代码中 `left + (right - left) / 2` 就和 `(left + right) / 2` 的结果相同，但是有效防止了 `left` 和 `right` 太大，直接相加导致溢出的情况。

## 寻找一个数

这个场景是最简单的，可能也是大家最熟悉的，即搜索一个数，如果存在，返回其索引，否则返回 -1。

```java
int binarySearch(int[] nums, int target) {
    int left = 0; 
    int right = nums.length - 1; // 注意
​
    while(left <= right) {
        int mid = left + (right - left) / 2;
        if(nums[mid] == target)
            return mid; 
        else if (nums[mid] < target)
            left = mid + 1; // 注意
        else if (nums[mid] > target)
            right = mid - 1; // 注意
    }
    return -1;
}
```

让我们深入探讨一下其中的细节。

**1. 为什么 while 循环的条件中是 <=，而不是 <？**

因为初始化 `right` 的赋值是 `nums.length - 1`，即最后一个元素的索引，而不是 `nums.length`。当`right = nums.length - 1`时，我们的搜索区间是闭区间 `[left, right]`，而当`right = nums.length`时，相当于左闭右开区间 `[left, right)`。因为索引大小为 `nums.length` 是越界的，所以我们把 `right` 这一边视为开区间。

我们在这个算法中使用的是 `[left, right]`这个闭区间。

那么什么时候停止搜索呢？有两种情况：找到了目标值和最后没有找到。

找到了目标值这个情况很好理解：

```java
if (nums[mid] == target)
    return mid; 
```

但是如果没有找到的话，需要等到while循环终止，并且返回-1。那么什么时候while循环终止呢？**在搜索空间为空的时候终止。**

注意这里while循环的条件是`while(left <= right)`，终止条件是`left == right + 1`，比如`[3, 2]`，这时的搜索空间为空，因为没有数字是既大于等于 3 又小于等于 2 的，这个时候循环终止，返回-1。

如果我们使用`while(left < right)`，终止条件变成了`left == right`，比如`[2, 2]`，我们发现这个区间不是空的，还有数字2，但这个时候循环终止了，我们并没有考虑到2这个数字，这是错误的。

当然，我们可以在返回时做一些修改，让我们可以使用这个条件，因为我们最后没有考虑到`left`这个数字，那我们在最后去考虑一下就可以了，如

```java
//...
while(left < right) {
    // ...
}
return nums[left] == target ? left : -1;
```

**2. 为什么 `left = mid + 1`，`right = mid - 1`？我看有的代码是 `right = mid` 或者 `left = mid`，没有这些加加减减，到底怎么回事，怎么判断？**

如果我们在上面明确了搜索区间这个概念，就会知道这个算法的搜索区间是一个`[left, right]`的闭区间，当我们发现`mid`不是我们要找的数字时，下一步自然是去搜索区间`[left, mid - 1]`或者`[mid + 1, right]`去搜索。因为`mid`我们已经查找过了，就应该从搜索区间中去掉。

**3. 这个算法有什么缺陷？**

比如说给你有序数组 `nums = [1,2,2,2,3]`，`target` 为 2，此算法返回的索引是 2，没错。但是如果我想得到 `target` 的左侧边界，即索引 1，或者我想得到 `target` 的右侧边界，即索引 3，这样的话此算法是无法处理的。

这个需求很常见，也许你会说，我们可以找到一个目标数字，然后向左或者向右去线性搜索。这个是可以的，但是我们很难保证二分查找`log2n`的时间复杂度。

## 寻找左侧边界

以下是最常见的代码形式。

```java
int left_bound(int[] nums, int target) {
    int left = 0;
    int right = nums.length; // 注意
    
    while (left < right) { // 注意
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            right = mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid; // 注意
        }
    }
    return left;
}
```

让我们深入探讨一下其中的细节。

**1. 为什么 while 中是 `<` 而不是 `<=`?**

同样用搜索区间来分析，因为`right == nums.length`而不是`nums.length - 1`，所以每次的搜索区间都是`[left, right)`这个左闭右开的区间。

这个区间的终止条件是`left == right`，比如`[2, 2)`，这个搜索区间为空。

> 这里再说一点，**刚才的 `right` 不是 `nums.length - 1` 吗，为啥这里非要写成 `nums.length` 使得「搜索区间」变成左闭右开呢**？
>
> 因为对于搜索左右侧边界的二分查找，这种写法比较普遍，也是大多数人的写法，理解这个写法方便你去理解别人的题解。如果非要用两边都闭的写法，反而更容易理解一些，我会在后面写上相关的代码。

**2. 为什么没有返回 -1 的操作？如果 `nums` 中不存在 `target` 这个值，怎么办？**

其实很简单，在返回的时候需要额外判断一下 `nums[left]` 是否等于 `target` 就行了，如果不等于，就说明 `target` 不存在。需要注意的是，访问数组索引之前要保证索引不越界。

```java
while (left < right) {
    //...
}
// 如果索引越界，说明数组中无目标元素，返回 -1
if (left < 0 || left >= nums.length) {
    return -1;
}
// 判断一下 nums[left] 是不是 target
return nums[left] == target ? left : -1;
```

> 需要注意的是，对于这个算法，我们不需要判断`left < 0`，因为`left`初始化为0，而且只可能往右边走，我们只可能在右边越界，即target大于这个数组中的所有数值。

**3. 为什么 `left = mid + 1`，`right = mid` ？和之前的算法不一样？**

因为我们的搜索区间是`[left, right)`左闭右开的，所以当我们查看过`nums[mid]`之后，下一步应该是去`mid`的左侧或右侧区间去搜索，即`[left, mid)`或者`[mid + 1, right)`。

**4. 这个算法为什么能找到左边边界？**

关键在于这部分代码

```java
if (nums[mid] == target) {
   right = mid;
```

我们在找到`target`时不会立刻返回，而是缩小搜索区间的上界`right`，在`[left, mid）`中继续搜索，不断向左收缩，直到到达左侧边界。

**5. 为什么返回left而不是right？**

其实都是一样的，因为while循环终止的条件是`left == right`。

**6. 那么能不能想办法把 `right` 变成 `nums.length - 1`，也就是继续使用两边都闭的「搜索区间」？这样就可以和第一种二分搜索在某种程度上统一起来了。**

当然时可以的，只要你明白搜索区间这个概念，能保证我们搜索了每个元素，肯定是没有问题的。那么如果搜索空间是闭区间，所以 `right` 应该初始化为 `nums.length - 1`，while 的终止条件应该是 `left == right + 1`，也就是其中应该用 `<=`。

```java
int left_bound(int[] nums, int target) {
    // 搜索区间为 [left, right]
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        // if else ...
    }
```

因为我们要搜索左侧边界，所以 `left` 和 `right` 的更新逻辑如下：

```java
if (nums[mid] < target) {
    // 搜索区间变为 [mid+1, right]
    left = mid + 1;
} else if (nums[mid] > target) {
    // 搜索区间变为 [left, mid-1]
    right = mid - 1;
} else if (nums[mid] == target) {
    // 收缩右侧边界
    right = mid - 1;
}
```

> 这个时候可能你又疑惑了，之前我们通过`right = mid`来收缩右边边界，而此时，在`nums[mid] == target`时，我们让`right = mid - 1`，这样怎么能找到target呢？答案是，我们并不是寻找target值，这个循环会终止在`left == right + 1`，right会指向`target左侧边界 - 1`这个索引，而left就是我们寻找的左侧边界。

完整的代码如下：

```java
int left_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    // 搜索区间为 [left, right]
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            // 搜索区间变为 [mid+1, right]
            left = mid + 1;
        } else if (nums[mid] > target) {
            // 搜索区间变为 [left, mid-1]
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 收缩右侧边界
            right = mid - 1;
        }
    }
    // 判断 target 是否存在于 nums 中
    // 如果越界，target 肯定不存在，返回 -1
    if (left < 0 || left >= nums.length) {
        return -1;
    }
    // 判断一下 nums[left] 是不是 target
    return nums[left] == target ? left : -1;
}
```

这种写法和第一种写法统一了，都是用的闭区间，这两种写法都是可以的。

## 寻找右侧边界

类似寻找左侧边界的算法，这里也会提供两种写法，还是先写常见的左闭右开的写法，只有两处和搜索左侧边界不同：

```java
int right_bound(int[] nums, int target) {
    int left = 0, right = nums.length;
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            left = mid + 1; // 注意
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid;
        }
    }
    return left - 1; // 注意
}
```

**1. 为什么这个算法能找到右侧边界？**

注意看这里

```java
if (nums[mid] == target) {
    left = mid + 1; // 注意
```

当 `nums[mid] == target` 时，不要立即返回，而是增大「搜索区间」的左边界 `left`，使得区间不断向右靠拢，达到锁定右侧边界的目的。

**2. 为什么最后返回 `left - 1` 而不像左侧边界的函数，返回 `left`？而且我觉得这里既然是搜索右侧边界，应该返回 `right` 才对。**

首先，while循环的终止条件是`left == right`，所以left和right是一样的。

为什么要减一呢？关键在于我们在增加左边界`left`的时候，让`left = mid + 1`，此时`mid`实际上等于`left - 1`。在循环结束的时候，`left`指向的数不是`target`，而是`target`右侧边界的下一个索引。

![1688494978046](file:///C:/Users/rensy/OneDrive/%E6%96%87%E6%A1%A3/WeChat%20Files/wxid\_9972xc0k1sbp22/FileStorage/Temp/1688494978046.png?lastModify=1688496381)

当然了，`target`值可能不存在与数组中，我们还是要判断一下的，类似之前的左侧边界搜索

```java
while (left < right) {
    // ...
}
// 判断 target 是否存在于 nums 中
// left - 1 索引越界的话 target 肯定不存在
if (left - 1 < 0 || left - 1 >= nums.length) {
    return -1;
}
// 判断一下 nums[left - 1] 是不是 target
return nums[left - 1] == target ? (left - 1) : -1;
```

**3. 是否也可以把这个算法的「搜索区间」也统一成两端都闭的形式呢？这样这三个写法就完全统一了。**

当然是可以的

```java
int right_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 这里改成收缩左侧边界即可
            left = mid + 1;
        }
    }
    // 最后改成返回 left - 1
    if (left - 1 < 0 || left - 1 >= nums.length) {
        return -1;
    }
    return nums[left - 1] == target ? (left - 1) : -1;
}
```

## 逻辑统一

让我们来梳理一下这些细节差异

**最基本的二分查找算法**

```javadoc
因为我们初始化 right = nums.length - 1
所以我们的搜索区间是 [left, right]
也就决定了 while (left < right) 以及 left = mid +1 和 right = mid - 1
    
因为我们这里只需要找到一个target就行
所以在nums[mid] == target的时候就可以返回了
```

**寻找左侧边界的二分查找**

```
因为我们初始化 right = nums.length
所以我们的搜索区间是 [left, right)
也就决定了 while (left < right) 和 left = mid = 1 和 right = mid
​
因为我们需要找到target的左侧索引
所以当nums[mid] == target时不要立刻返回
而是收缩右侧边界来锁定左侧边界   
```

**寻找右侧边界的二分查找**

```
因为我们初始化 right = nums.length
所以我们的搜索区间是 [left, right)
也就决定了 while (left < right) 和 left = mid = 1 和 right = mid
            
因为我们需找到 target 的最右侧索引
所以当 nums[mid] == target 时不要立即返回
而要收紧左侧边界以锁定右侧边界
​
又因为收紧左侧边界时必须 left = mid + 1
所以最后无论返回 left 还是 right，必须减一
```

对于寻找左右边界的二分查找，最常见的就是使用左闭右开的搜索区间，当然我们还可以使用闭区间，只需要修改几处即可：

```java
int binary_search(int[] nums, int target) {
    int left = 0, right = nums.length - 1; 
    while(left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1; 
        } else if(nums[mid] == target) {
            // 直接返回
            return mid;
        }
    }
    // 直接返回
    return -1;
}
​
int left_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 别返回，锁定左侧边界
            right = mid - 1;
        }
    }
    // 判断 target 是否存在于 nums 中
    if (left < 0 || left >= nums.length) {
        return -1;
    }
    // 判断一下 nums[left] 是不是 target
    return nums[left] == target ? left : -1;
}
​
int right_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 别返回，锁定右侧边界
            left = mid + 1;
        }
    }
    // 判断 target 是否存在于 nums 中
    // if (left - 1 < 0 || left - 1 >= nums.length) {
    //     return -1;
    // }
    
    // 由于 while 的结束条件是 right == left - 1，且现在在求右边界
    // 所以用 right 替代 left - 1 更好记
    if (right < 0 || right >= nums.length) {
        return -1;
    }
    return nums[right] == target ? right : -1;
}
```

总结一下需要注意的点

1. 分析二分查找代码时，不要出现 else，全部展开成 else if 方便理解。
2. 注意「搜索区间」和 while 的终止条件，如果存在漏掉的元素，记得在最后检查。
3. 如需定义左闭右开的「搜索区间」搜索左右边界，只要在 `nums[mid] == target` 时做修改即可，搜索右侧时需要减一。
4. 如果将「搜索区间」全都统一成两端都闭，好记，只要稍改 `nums[mid] == target` 条件处的代码和返回的逻辑即可。

## 总结

> **总框架**

```java
int binarySearch(int[] nums, int target) {
    int left = 0, right = ...;
​
    while(...) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            ...
        } else if (nums[mid] < target) {
            left = ...
        } else if (nums[mid] > target) {
            right = ...
        }
    }
    return ...;
}
```

#### 搜索某个数值

**闭区间写法**

```java
int binarySearch(int[] nums, int target) {
    int left = 0; 
    int right = nums.length - 1; // 注意 这里right的值决定了middle的取值范围和查找操作
​
    while(left <= right) {
        int mid = left + (right - left) / 2; //是为了防止数值溢出
        if(nums[mid] == target)
            return mid; 
        else if (nums[mid] < target)
            left = mid + 1; // 注意   选择区间[mid+1,right]
        else if (nums[mid] > target)
            right = mid - 1; // 注意  选择区间[left,mid+1]
    }
    return -1;
}
```

**左闭右开写法**

```java
int binarySearch(int[] nums, int target) {
    int left = 0; 
    int right = nums.length; // 注意 这里right的值决定了middle的取值范围和查找操作
​
    while(left < right) {
        int mid = left + (right - left) / 2; //是为了防止数值溢出
        if(nums[mid] == target)
            return mid; 
        else if (nums[mid] < target)
            left = mid + 1; // 注意   选择区间[mid+1,right)
        else if (nums[mid] > target)
            right = mid ; // 注意  选择区间[left,mid)
    }
    return -1;
}
```

#### 寻找左边界

> left 定位，right 移动

**左闭右开写法**

```java
int left_bound(int[] nums, int target) {
    int left = 0;
    int right = nums.length; // 注意  
    
    while (left < right) { // 注意  [left,right)
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
              right = mid;  //当搜索到新的位于right左边新的target值时，right过去指向它，保证了right永远指向左边界
        } else if (nums[mid] < target) {
            left = mid + 1;  //当right为target时，停止条件为left=right，所以此函数结束时，left就是指向左边界。
        } else if (nums[mid] > target) {
            right = mid; // 注意   为了接近target 选择到[left,mid)
        }
    }
    return left;
}
```

闭区间写法

```java
int left_bound(int[] nums, int target) {
    int left = 0;
    int right = nums.length-1; // 注意  
    
    while (left <= right) { // 注意  [left,right]
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            right = mid-1;  //当搜索到新的位于right左边新的target值时，right过去指向它位置的左边，保证了right永远指向左边界的左边。为什么要让它指向左边呢，因为是闭区间，right容易等于mid，导致死循环，所以必须把right向左收缩
        } else if (nums[mid] < target) {
            left = mid + 1;  //当right为target左边界左边时，停止条件为left>right，所以此函数结束时，left就是指向target左边界。     选择到[mid+1,right]
        } else if (nums[mid] > target) {
            right = mid-1; // 注意   为了接近target   [left,mid-1]
        }
    }
    return left;
}
```

#### 寻找右边界

> **right移动，left定位。**

**左闭右开写法**

```java
int right_bound(int[] nums, int target) {
    int left = 0, right = nums.length;
    
    while (left < right) { // [left,right)
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            left = mid + 1; // 注意  为了让left向右收缩到target右边界的右边，若代码为left=mid，则在特殊情况容易导致死循环，比如left和right紧挨着，他们两个都指向了target值。   
        } else if (nums[mid] < target) {
            left = mid + 1;// 为了接近target  选择到[mid+1,right)
        } else if (nums[mid] > target) {
            right = mid;// 常规操作    选择到[left,mid)
        }
    }
    return left - 1; // 注意   函数停止条件是left=right，所以在函数停止时，right和left都在target右边界的右边，所以返回的时候要返回left-1或者right-1
}
```

**闭区间写法**

```java
int right_bound(int[] nums, int target) {
    int left = 0, right = nums.length-1;
    
    while (left <= right) { // [left,right]
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            left = mid + 1; // 注意  为了让left向右收缩到target右边界的右边，若代码为left=mid，则在特殊情况容易导致死循环，比如left和right紧挨着，他们两个都指向了target值。   
        } else if (nums[mid] < target) {
            left = mid + 1;// 为了接近target   选择到[mid+1,right]
        } else if (nums[mid] > target) {
            right = mid-1;// 常规操作    选择到[left,mid-1]
        }
    }
    return left - 1; // 注意   函数停止条件是left=right，所以在函数停止时，right和left都在target右边界的右边，所以返回的时候要返回left-1或者right-1
}
```

#### 特殊技巧

```java
int right_bound(int[] nums, int target) {
    int left = 0, right = nums.length-1;
    
    while (left <= right) { // [left,right]
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            left = mid + 1;   
            ans=mid  //是为了记录右边界,即最后的right值。
      
        } else if (nums[mid] < target) {
            left = mid + 1;// 为了接近target   选择到[mid+1,right]
        } else if (nums[mid] > target) {
            right = mid-1;// 常规操作    选择到[left,mid-1]
        }
    }
    return mid; // 注意   返回右边界
}
```

