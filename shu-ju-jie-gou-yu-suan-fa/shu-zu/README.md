# 数组

## 定义

数组是由一组元素（值或变量）组成的数据结构，每个元素有至少一个索引或键来标识。因为数组内的元素是**连续存储**的，所以数组中元素的地址，可以通过其索引计算出来，例如：

`int[] array = {1,2,3,4,5}`

知道了数组的数据起始地址 BaseAddress，就可以由公式 <mark style="background-color:orange;">BaseAddress + i \* size</mark> 计算出索引 i 元素的地址

* i 即索引，在 Java、C 等语言都是从 0 开始
* size 是每个元素占用字节，例如 <mark style="background-color:orange;">byte占1个字节，int 占 4个字节，double 占 8个字节。</mark>

例子：

`byte[] array = {1,2,3,4,5}`

已知 array 的数据的起始地址是 0x7138f94c8，那么元素 3 的地址是什么？

0x7138f94c8 + 2 \* 1 = 0x7138f94ca



## 空间占用

Java 中数组结构为

* 8 字节 markword
* 4 字节 class 指针（压缩 class 指针的情况）
* 4 字节 数组大小（决定了数组最大容量是 2^{32}）
* 数组元素 + 对齐字节（java 中所有对象大小都是 8 字节的整数倍，不足的要用对齐字节补足）

例如

```java
int[] array = {1, 2, 3, 4, 5};
```

的大小为 40 个字节，组成如下

```java
8 + 4 + 4 + 5*4 + 4(alignment)
```



## 随机访问性能

即根据索引查找元素，<mark style="background-color:orange;">时间复杂度是 O(1)</mark>



## 动态数组

数组的增、删、改、查

<mark style="background-color:purple;">遍历的部分我需要重新回去看一下课程</mark>

<pre class="language-java"><code class="lang-java">public class DynamicArray implements Iterable&#x3C;Integer> {
    private int size = 0; // 逻辑大小
    private int capacity = 8; // 容量
    private int[] array = {};


    /**
     * 向最后位置 [size] 添加元素
     *
     * @param element 待添加元素
     */
    public void addLast(int element) {
        add(size, element);
    }

    /**
     * 向 [0 .. size] 位置添加元素
     *
     * @param index   索引位置
     * @param element 待添加元素
     */
    public void add(int index, int element) {
        checkAndGrow();// 容量检查

        // 添加逻辑
        if (index >= 0 &#x26;&#x26; index &#x3C; size) {
            // 向后挪动, 空出待插入位置
            System.<a data-footnote-ref href="#user-content-fn-1">arraycopy</a>(array, index,
                    array, index + 1, size - index);
        }
        array[index] = element;
        size++;
    }

    private void checkAndGrow() {
        // 容量检查
        if (size == 0) {
            array = new int[capacity];
        } else if (size == capacity) {
            // 进行扩容, 1.5倍 
            capacity += capacity >> 1;
            int[] newArray = new int[capacity];
            System.arraycopy(array, 0,
                    newArray, 0, size);
            array = newArray;
        }
    }

    /**
     * 从 [0 .. size) 范围删除元素
     *
     * @param index 索引位置
     * @return 被删除元素
     */
    public int remove(int index) { // [0..size)
        int removed = array[index];
        if (index &#x3C; size - 1) {//如果是最后一个index就不需要向前移动
            // 向前挪动
            System.arraycopy(array, index + 1,
                    array, index, size - index - 1);
        }
        size--;
        return removed;
    }


    /**
     * 查询元素
     *
     * @param index 索引位置, 在 [0..size) 区间内
     * @return 该索引位置的元素
     */
    public int get(int index) {
        return array[index];
    }

    /**
     * 遍历方法1
     *
     * @param consumer 遍历要执行的操作；入参: 每个元素
     */
    public void foreach(Consumer&#x3C;Integer> consumer) {
        for (int i = 0; i &#x3C; size; i++) {
            // 提供 array[i]
            // 返回 void
            consumer.accept(array[i]);
        }
    }

    /**
     * 遍历方法2 - 迭代器遍历
     */
    @Override
    public Iterator&#x3C;Integer> iterator() {
        return new Iterator&#x3C;Integer>() {
            int i = 0;

            @Override
            public boolean hasNext() { // 有没有下一个元素
                return i &#x3C; size;
            }

            @Override
            public Integer next() { // 返回当前元素,并移动到下一个元素
                return array[i++];
            }
        };
    }

    /**
     * 遍历方法3 - stream 遍历
     *
     * @return stream 流
     */
    public IntStream stream() {
        return IntStream.of(Arrays.copyOfRange(array, 0, size));
    }
}
</code></pre>



## 插入或删除性能

<mark style="background-color:orange;">头部位置，时间复杂度是 O(n)</mark>

<mark style="background-color:orange;">中间位置，时间复杂度是 O(n)</mark>

<mark style="background-color:orange;">尾部位置，时间复杂度是 O(1)</mark>

[^1]: 举例：arrayCopy( arr1, 2, arr2, 5, 10); 意思是: 将arr1数组里从索引为2的元素开始，复制到数组arr2里的索引为5的位置，复制的元素个数为10个
