# 怎么选择Python并发API

python标准库有三个并发API。

那么我们要如何选择不同的API呢？

python并发API，包括基于进程的`multiprocessing`模块，基于线程的`threading`模块和基于协程的`asyncio`模块。在做选择的时候，我们不仅要考虑选择基于什么的模块，同时也要考虑我们需不需要线程/进程池。

<figure><img src="https://superfastpython.com/wp-content/uploads/2022/07/Python-Concurrency-API-Choice.jpg" alt=""><figcaption></figcaption></figure>

## 选择Python并发API的步骤

具体的步骤如下：

1. CPU密集还是IO密集？(多进程vs多线程)
2. 如果用线程，选择用线程还是协程
3. 多个任务还是一个复杂的任务？
4. 用Pools还是用Executors？

<figure><img src="https://superfastpython.com/wp-content/uploads/2022/07/Python-Concurrency-API-Decision-Tree.jpg" alt=""><figcaption></figcaption></figure>

## CPU密集还是IO密集？

#### CPU密集

CPU密集任务主要是计算任务，不包含很多IO操作。这种任务的限制在于CPU的计算速度。

比如：

* 估计Pi
* 处理文本文件
* Parse文档

CPU速度很快，而且我们通常有多个CPU，我们希望可以充分利用多个CPU核心来完成我们的任务。

#### IO密集

IO密集任务包含从设备、文件系统或者网络连接去读写数据，这种任务的瓶颈在于IO的速度，取决于设备、硬盘和网络连接。

CPU的速度是很快的，相较于CPU操作，IO操作是非常慢的。IO操作需要系统内核指令，如果你的CPU主要处理这种操作，即IO操作都是在main线程执行的，那么你的CPU很可能会等待几百微秒，甚至几秒钟，什么都不做，这浪费了CPU进行计算的时间。我们需要避免这种情况发生。

#### 选择并发API

一般来说，对于CPU密集型任务，我们选择`multiprocessing`模块，对于IO密集型任务，我们选择基于线程的并发。

<figure><img src="https://superfastpython.com/wp-content/uploads/2022/07/Python-Concurrency-API-threading-vs-multiprocessing.jpg" alt=""><figcaption></figcaption></figure>

#### 选择线程和协程

对于IO密集型任务，我们可以用线程和协程来并发。通常来说，对于有多个socket连接的情况下，我们更希望用协程，其他的情况我们会使用线程。

`asyncio`模块主要用于socket连接中的非阻塞IO，比如说，如果你的任务是基于文件的，那么异步并不是一个很好的选择。

协程相较于线程更加lightweight，所以一个线程中的协程数量可以多于一个进程中的线程数。另一个值得考虑的点是你的任务是否需要以异步的形式去编写。

## 单个任务还是多个任务？

这里我们关注的是我们是否需要利用线程/进程池来运行多个任务，对于单个任务来说，池有些没必要了。同时，如果你的任务非常复杂，比如监听器，调度器等等，会持续运行非常长的时间，那么我们也不需要池来复用多个线程/进程。

* 短的/数量多的任务：用线程/进程池
* 长的/数量少的任务：用单个线程/进程

其他的我们可以考虑的地方有，对于多个不同的任务，或者我们希望复用多个线程，我们可以用线程池，否则我们可以直接使用线程类。

<figure><img src="https://superfastpython.com/wp-content/uploads/2022/07/Python-Concurrency-API-Worker-Pool-vs-Class.png" alt=""><figcaption></figcaption></figure>

## Pools还是Executors?

第三步是考虑用什么来实现worker池。主要有两种方式：

* Pool： **multiprocessing.pool.Pool**，**multiprocessing.pool.ThreadPool**
* Executors： **concurrent.futures.Executor**

他们相同的点有

* 都有线程池和进程池
* 都支持同步和异步的操作
* 都可以检查线程的状态，并且等待异步操作
* 都有callback函数

选择不同的实现方式并不会对你的函数产生非常大的影响，但是他们的API还是有一些区别的：

* Executors 可以取消任务
* Executors 可以让线程/进程进行一组不同的任务
* Pool 可以强制终止所有任务
* Executors 可以获取任务中的异常

最主要的区别在于，Pool主要关注在for循环中执行并行的多个map()任务，比如对同一个函数传入不同的参数。Executors 主要关注于异步的执行随机任务并且管理这个任务集合。
