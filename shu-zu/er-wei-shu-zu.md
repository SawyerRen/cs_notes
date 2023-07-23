# 二维数组

## 局部性原理

只讨论空间局部性

* cpu 读取内存（速度慢）数据后，会将其放入高速缓存（速度快）当中，如果后来的计算再用到此数据，在缓存中能读到的话，就不必读内存了。
* 缓存的最小存储单位是**缓存行**（cache line），一般是 64 bytes，一次读的数据少了不划算，因此最少读 64 bytes 填满一个缓存行，因此读入某个数据时也会读取其**临近的数据**，这就是所谓**空间局部性。**



## 对效率的影响

比较下面 ij 和 ji 两个方法的执行效率

```java
int rows = 1000000;
int columns = 14;
int[][] a = new int[rows][columns];

StopWatch sw = new StopWatch();
sw.start("ij");//始一个无名称的任务的计时。 
ij(a, rows, columns);
sw.stop();//停止当前任务的计时
sw.start("ji");
ji(a, rows, columns);
sw.stop();
System.out.println(sw.prettyPrint());//打印所有任务的详细耗时情况
```

```java
public static void ij(int[][] a, int rows, int columns) {
    long sum = 0L;
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < columns; j++) {
            sum += a[i][j];
        }
    }
    System.out.println(sum);
}
```

```java
public static void ji(int[][] a, int rows, int columns) {
    long sum = 0L;
    for (int j = 0; j < columns; j++) {
        for (int i = 0; i < rows; i++) {
            sum += a[i][j];
        }
    }
    System.out.println(sum);
}
```

```
0
0
StopWatch '': running time = 96283300 ns
---------------------------------------------
ns         %     Task name
---------------------------------------------
016196200  017%  ij
080087100  083%  ji
```

<mark style="background-color:orange;">ij 的效率比 ji 快很多</mark>

* <mark style="background-color:orange;">缓存是有限的，当新数据来了后，一些旧的缓存行数据就会被覆盖</mark>
* <mark style="background-color:orange;">如果不能充分利用缓存的数据，就会造成效率低下</mark>

<figure><img src="../.gitbook/assets/截屏2023-07-17 21.32.14.png" alt=""><figcaption><p>提前缓存行</p></figcaption></figure>

<figure><img src="../.gitbook/assets/截屏2023-07-17 21.37.47.png" alt=""><figcaption><p>缓存的数据用不上 持续被迭代</p></figcaption></figure>

