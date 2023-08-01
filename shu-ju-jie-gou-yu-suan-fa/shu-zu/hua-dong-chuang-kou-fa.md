# 滑动窗口法

## 算法和框架

滑动窗口法也是一种双指针算法，单独拿出来是因为这个算法相对比较复杂。思路是很简单的，维护一个窗口，不断的滑动，如何更新答案。大致是这样的：

```java
int left = 0, right = 0;

while (left < right && right < arr.length) {
    // 增大窗口
    window.add(arr[right]);
    right++;
    
    while (window needs shrink) {
        // 缩小窗口
        window.remove(arr[left]);
        left++;
    }
}
```

看上去思路很简单，但是又很多细节问题，比如说如何向窗口添加新元素，如何缩小窗口，什么时候更新结果。下面给出一个滑动窗口的代码框架，几乎所有的滑动窗口题目都可以用以下的框架来改写：

```java
/* 滑动窗口算法框架 */
void slidingWindow(String s) {
    // 用合适的数据结构记录窗口中的数据
    HashMap<Character, Integer> window = new HashMap<>();

    int left = 0, right = 0;
    while (right < s.length()) {
        // c 是将移入窗口的字符
        char c = s.charAt(right);
        window.put(c, window.getOrDefault(c, 0) + 1);
        // 增大窗口
        right++;
        // 进行窗口内数据的一系列更新
        ...

        /*** debug 输出的位置 ***/
        // 注意在最终的解法代码中不要 print
        // 因为 IO 操作很耗时，可能导致超时
        System.out.printf("window: [%d, %d)\n", left, right);
        /********************/

        // 判断左侧窗口是否要收缩
        while (left < right && window needs shrink) {
            // d 是将移出窗口的字符
            char d = s.charAt(left);
            window.put(d, window.get(d) - 1);
            // 缩小窗口
            left++;
            // 进行窗口内数据的一系列更新
            ...
        }
    }
}
```

上面的`...`就是更新窗口数据的地方，这两处分别是扩大窗口和缩小窗口的更新操作，是完全对称的。

需要注意的是，虽然代码中存在嵌套的while循环，但这个算法的复杂度还是O(N)，为什么？因为指针left和right都不会后退，字符串/数组的元素都只会进入窗口一次，然后离开窗口一次，时间复杂度就和字符串的长度成正比。

当然，上面的写法是一个统一的框架，实际的题目可能存在更加简化的写法。

下面来看一些题目

## 相关题目

### [leetcode76](https://leetcode.com/problems/minimum-window-substring/description/)

这题要我们找到s中的一个子串，其中包含了t中出现的所有字符，并且这个子串是满足条件的子串中最短的，第一直觉的解法：

```java
for (int i = 0; i < s.length(); i++)
    for (int j = i + 1; j < s.length(); j++)
        if s[i:j] 包含 t 的所有字母:
            更新答案

```

这个解法的时间复杂度起码是O(n^2)，不是最优解。那么滑动窗口法的思路是什么样的呢？

1. 我们在字符串 `S` 中使用双指针中的左右指针技巧，初始化 `left = right = 0`，把索引**左闭右开**区间 `[left, right)` 称为一个「窗口」。
2. 我们先不断地增加 `right` 指针扩大窗口 `[left, right)`，直到窗口中的字符串符合要求（包含了 `T` 中的所有字符）。
3. 此时，我们停止增加 `right`，转而不断增加 `left` 指针缩小窗口 `[left, right)`，直到窗口中的字符串不再符合要求（不包含 `T` 中的所有字符了）。同时，每次增加 `left`，我们都要更新一轮结果。
4. 重复第 2 和第 3 步，直到 `right` 到达字符串 `S` 的尽头。

其实我们在第二步寻找一个可行解，并且在第三步中优化这个可行解，寻找最短的子串。

那么我们怎么通过上面的框架来解这道题呢？

我们需要考虑这些问题：什么时候扩大窗口呢？扩大的时候应该更新什么数据？什么时候窗口开始缩小？缩小的时候又该怎么更新数据？

```java
class Solution {
    public String minWindow(String s, String t) {
        // 记录窗口中的字符出现次数
        Map<Character, Integer> map = new HashMap<>();
        // 需要的字符出现的次数
        Map<Character, Integer> need = new HashMap<>();
        // 统计t中的字符数量
        for (char c : t.toCharArray()) {
            need.put(c, need.getOrDefault(c, 0) + 1);
        }
        // left是左指针，right是右指针，minLen表示当前找到的最小窗口大小，count表示窗口中满足的字符数量
        int left = 0, right = 0, minLen = s.length() + 1, count = 0;
        // start和end是结果字符串的开头/结尾index
        int start = 0, end = 0;
        while (right < s.length()) {
            // rc 是将移入窗口的字符
            char rc = s.charAt(right);
            // 增大窗口
            right++;
            // 窗口内数据的更新
            if (need.containsKey(rc)) {
                map.put(rc, map.getOrDefault(rc, 0) + 1);
                // 当窗口中的当前字符出现的次数和需要的数量一致时，我们找到了一个满足条件的字符
                if (map.get(rc).equals(need.get(rc))) {
                    count++;
                }
            }
            // 满足条件后，尝试缩小窗口
            while (count == need.size()) {
                // 更新最小长度和结果index
                if (right - left < minLen) {
                    minLen = right - left;
                    start = left;
                    end = right;
                }
                // lc是要移除的字符
                char lc = s.charAt(left);
                // 缩小窗口
                left++;
                // 窗口内数据的更新
                if (need.containsKey(lc)) {
                    // 只有在次数相同的时候，表示我们失去了一个满足条件的字符
                    if (map.get(lc).equals(need.get(lc))) {
                        count--;
                    }
                    map.put(lc, map.get(lc) - 1);
                }
            }
        }
        return s.substring(start, end);
    }
}
```

还有一种写法，总体思路是一致的，具体的写法不同，可以挑好理解的写。

```java
class Solution {
    public String minWindow(String s, String t) {
        // 记录t中字符的数量
        Map<Character, Integer> map = new HashMap<>();
        for (char c : t.toCharArray()) {
            map.put(c, map.getOrDefault(c, 0) + 1);
        }
        // i是左指针，j是右指针，count表示我们要找的字符数量
        int i = 0, j = 0, count = t.length(), minLen = s.length() + 1;
        // 结果index
        int left = 0, right = 0;
        while (j < s.length()) {
            // 要加入窗口的字符
            char rc = s.charAt(j);
            // 窗口内操作
            if (map.containsKey(rc)) {
                map.put(rc, map.get(rc) - 1);
                // 只有map中rc的数量是正数的时候，说明我们找到了t中的一个字符，count--
                if (map.get(rc) >= 0) count--;
            }
            // 扩大窗口
            j++;
            // count==0时，找到了满足条件的子串
            while (count == 0) {
                if (j - i < minLen) {
                    minLen = j;
                    left = i;
                    right = j;
                }
                // 要删除的字符
                char lc = s.charAt(i);
                // 窗口内操作
                if (map.containsKey(lc)) {
                    map.put(lc, map.get(lc) + 1);
                    // 只有map中的数量是正数的时候，说明我们找到了t中的字符，count++
                    if (map.get(lc) >= 1) count++;
                }
                // 缩小窗口
                i++;
            }
        }
        return s.substring(left, right);
    }
}
```

### [leetcode567](https://leetcode.com/problems/permutation-in-string/description/)

我们发现这道题和上面这道题很相似，要在s2中找到一个子串包含且仅包含s1中的所有字符，只有在结果操作的时候有不同，这里直接附上答案。

```java
class Solution {
    public boolean checkInclusion(String s1, String s2) {
        Map<Character, Integer> map = new HashMap<>();
        Map<Character, Integer> need = new HashMap<>();
        for (char c : s1.toCharArray()) {
            need.put(c, need.getOrDefault(c, 0) + 1);
        }
        int left = 0, right = 0, count = 0;
        while (right < s2.length()) {
            char rc = s2.charAt(right);
            right++;
            if (need.containsKey(rc)) {
                map.put(rc, map.getOrDefault(rc, 0) + 1);
                if (need.get(rc).equals(map.get(rc))) {
                    count++;
                }
            }
            while (count == need.size()) {
                // 在我们找到s1的所有字符时，还要判断子串的长度是否等于s1的长度，以防出现额外的字符
                if (right - left == s1.length()) return true;
                char lc = s2.charAt(left);
                left++;
                if (need.containsKey(lc)) {
                    if (map.get(lc).equals(need.get(lc))) {
                        count--;
                    }
                    map.put(lc, map.get(lc) - 1);
                }
            }
        }
        return false;
    }
}
```

当然还有另一种写法

```java
class Solution {
    public boolean checkInclusion(String s1, String s2) {
        Map<Character, Integer> map = new HashMap<>();
        int i = 0, j = 0, count = s1.length();
        for (char c : s1.toCharArray()) {
            map.put(c, map.getOrDefault(c, 0) + 1);
        }
        while (j < s2.length()) {
            char rc = s2.charAt(j);
            if (map.containsKey(rc)) {
                if (map.get(rc) > 0) count--;
                map.put(rc, map.get(rc) - 1);
            }
            j++;
            while (count == 0) {
                if (j - i == s1.length()) return true;
                char lc = s2.charAt(i);
                if (map.containsKey(lc)) {
                    if (map.get(lc) >= 0) count++;
                    map.put(lc, map.get(lc) + 1);
                }
                i++;
            }
        }
        return false;
    }
}
```

### [leetcode438](https://leetcode.com/problems/find-all-anagrams-in-a-string/description/)

这题跟上面的题目就更相似了，区别只是要找到所有满足条件子串的开始index。这里直接给出两种写法

```java
public List<Integer> findAnagrams(String s, String t) {
    Map<Character, Integer> need = new HashMap<>();
    Map<Character, Integer> window = new HashMap<>();
    for (int i = 0; i < t.length(); i++) {
        char c = t.charAt(i);
        need.put(c, need.getOrDefault(c, 0) + 1);
    }

    int left = 0, right = 0;
    int valid = 0;
    List<Integer> res = new ArrayList<>(); // 记录结果
    while (right < s.length()) {
        char c = s.charAt(right);
        right++;
        // 进行窗口内数据的一系列更新
        if (need.containsKey(c)) {
            window.put(c, window.getOrDefault(c, 0) + 1);
            if (window.get(c).equals(need.get(c))) {
                valid++;
            }
        }
        // 判断左侧窗口是否要收缩
        while (right - left >= t.length()) {
            // 当窗口符合条件时，把起始索引加入 res
            if (valid == need.size()) {
                res.add(left);
            }
            char d = s.charAt(left);
            left++;
            // 进行窗口内数据的一系列更新
            if (need.containsKey(d)) {
                if (window.get(d).equals(need.get(d))) {
                    valid--;
                }
                window.put(d, window.get(d) - 1);
            }
        }
    }
    return res;
}
```

```java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        Map<Character, Integer> map = new HashMap<>();
        List<Integer> res = new ArrayList<>();
        int i = 0, j = 0, count = p.length();
        for (char c : p.toCharArray()) {
            map.put(c, map.getOrDefault(c, 0) + 1);
        }
        while (j < s.length()) {
            char rc = s.charAt(j);
            if (map.containsKey(rc)) {
                if (map.get(rc) > 0) count--;
                map.put(rc, map.get(rc) - 1);
            }
            j++;
            while (count == 0) {
                if (j - i == p.length()) {
                    res.add(i);
                }
                char lc = s.charAt(i);
                if (map.containsKey(lc)) {
                    if (map.get(lc) >= 0) count++;
                    map.put(lc, map.get(lc) + 1);
                }
                i++;
            }
        }
        return res;
    }
}
```

### [leetcode3](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

这题要我们找到不包含重复字符的最长子串，这题不能直接用上面的框架，但是更加简单了。

```java
int lengthOfLongestSubstring(String s) {
    Map<Character, Integer> window = new HashMap<>();

    int left = 0, right = 0;
    int res = 0; // 记录结果
    while (right < s.length()) {
        char c = s.charAt(right);
        right++;
        // 进行窗口内数据的一系列更新
        window.put(c, window.getOrDefault(c, 0) + 1);
        // 判断左侧窗口是否要收缩
        while (window.get(c) > 1) {
            char d = s.charAt(left);
            left++;
            // 进行窗口内数据的一系列更新
            window.put(d, window.get(d) - 1);
        }
        // 在这里更新答案
        res = Math.max(res, right - left);
    }
    return res;
}

```

甚至我们还可以更加简化一些，既然只要窗口中不重复，我们用HashSet就可以进行去重，在扩大窗口之前先去掉窗口中重复的字符。

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        Set<Character> set = new HashSet<>();
        int i = 0, j = 0;
        int res = 0;
        while (j < s.length()) {
            char rc = s.charAt(j);
            // 在将rc放入窗口中之前，先保证rc加入之后不会重复
            // 通过缩小窗口并去掉所有等于rc的元素来完成
            while (set.contains(rc)) {
                set.remove(s.charAt(i++));
            }
            // 现在我们可以放心的加入rc
            set.add(rc);
            // set的大小其实就是窗口的大小，因为窗口内没有重复值
            res = Math.max(res, set.size());
            j++;
        }
        return res;
    }
}
```
