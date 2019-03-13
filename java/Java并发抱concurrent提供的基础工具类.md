## <center>  java并发抱java.util.concurrent提供的基础工具类 </center>

### 一. 同步结构

提供了比synchronized更加高级的同步结构：countDownLatch、CyclicBarrier、Semaphore等，可以实现更加丰富的多线程操作。

1. Semaphore：作为资源控制器限制同时进行工作的线程数量，java版本的信号量实现。

   [通过Semaphore实现车站调度demo](https://github.com/wenPKtalk/mutithread/blob/master/src/main/java/current_demo/AbnormalSemaphoreSample.java)

2. CountDownLatch：允许一个或者多个线程等待某些操作完成。

3. CyclicBarrier：一种辅助性的同步结构，语序多个线程等待到达某个屏障。

   CountDownLatch和CyclicBarrier的区别：

   i. CountDownLatch是不可以重置，所以无法重用，而CyclicBarrier则没有这种限制，可以重用。

   ii. CountDownLatch的基本操作组合是countDown/await。调用await的线程阻塞等待countDown足够的次数，不管你是在一个线程还是多个线程里countDown,只要次数足够即可。CountDownLatch操作的是事件。

   iii. CyclicBarrier的基本操作组合，则就是await,当所有的伙伴（parties）都调用了await,才会继续进行任务，并自动进行重置。注意，正常情况下，CyclicBarrier的重置都是自动发生的，如果我们调用reset方法，单还有线程在等待，就会导致等待线程发生干扰，抛出BrokenBarrierException异常。CyclicBarrier侧重点是线程，而不是调用事件， **它的典型应用场景是用来等待并发线程结束。**

### 二. 线程安全容器

**java.util.concurrent 包提供的容器（Queue、List、Set）、Map，从命名上可以大概区分为 Concurrent*、CopyOnWrite和 Blocking**

**Map形式的：**

1. ConcurrentHashMap：jdk8以前使用分段锁，jdk8后采用CAS

2. ConcunrrentSkipListMap：
3. ConcurrentSkipListMap：是TreeMap的线程安全版本。

**List形式的：**

CopyOnWriteArrayList：通过快照实现，适用于读多写少的场景。在对其实例进行修改操作（add/remove等）会新建一个数据并修改，修改完毕之后，再将原来的引用指向新的数组。

**Set形式的：**

CopyOnWriteArraySet：同上CopyOnWriteArrayList

**Queue形式的：**

[](!)



### 三. 并发队列

BlockedQueue：ArrayBlockedQueue,SynchorousQueue

### 四. 强大的Executor框架

