<https://javabetter.cn/sidebar/sanfene/javathread.html>

## 进程

1.  正在执行的程序实例，代表一段程序进行状态
2.  共享内存、系统资源的管理单元

进程本身不包含实际的代码。进程是操作系统的一个概念和实体，它代表了一个正在执行的程序实例。具体的代码是在程序中，当程序运行时，操作系统会为其创建一个进程，为其分配资源，并管理其执行。

在这个过程中，操作系统会使用一些数据结构来表示和管理进程，比如进程控制块（PCB）等。这些数据结构会记录进程的信息，如进程状态、进程ID、程序计数器、CPU寄存器、内存管理信息等。

操作系统的内核代码会管理进程的创建、调度、销毁等操作。这些内核代码是操作系统的一部分，而不是进程本身的一部分。进程本身只是一个抽象的概念和实体，代表了一个正在执行的程序实例。

## 并行vs并发

并行：多核cpu同时运行

并发：处理多个任务的能力，同一时间只有一个任务在运行，单核处理器上通过时间切片轮转 轮流执行来实现

## 线程分类

1.  守护 daemon

    *   垃圾回收线程
2.  用户 user

    *   main方法所在线程

        当最后一个非守护线程束时， JVM 会正常退出，而不管当前是否存在守护线程

## Java Thread

线程vs 线程类

线程：操作系统CPU资源分配的调度单元，抽象概念

Thread：本质仍是可执行代码，用于完成“线程管理”这个任务

1.  implements Runnable
2.  实际上在新线程上运行的只有runnable run方法

## 创建线程的几种方式

1.  继承Thread

    *   重写run方法，调用start
    *   只能实现一次
2.  实现runnable接口

    *   重写run方法，创建thread，入参数，调用start
    *   Runnable 接口将任务代码和线程控制的代码解耦了
    *   避免单继承限制
3.  实现callable接口

    *   实现 Callable 接口，重写 `call()` 方法，然后创建 FutureTask 对象，参数为 Callable 对象；紧接着创建 Thread 对象，参数为 FutureTask 对象，调用 `start()` 方法启动线程。
    *   可以获取线程的执行结果。

## 线程调度方法

1.  等待

    *   wait 直到共享对象的notify/notifyAll被调用时 或者interrupt被调用抛出异常

        *   `wait()` 方法必须在同步代码块或同步方法中被调用，这是因为调用 `wait()` 方法的前提是当前线程必须持有对象的锁。否则会抛出 `IllegalMonitorStateException` 异常。
    *   thread的join方法，等待线程终止
2.  通知

    *   notify
    *   notifyAll
3.  休眠

    *   sleep 让出执行权 x时间
4.  让出优先权

    *   yield （提示作用）

        *   用于提示线程调度器（Thread Scheduler）当前线程愿意让出对CPU的使用权。这个方法的作用是使当前线程从`Runnable`状态回到线程调度器的调度队列中，从而允许调度器选择另一个同优先级的线程来执行。
        *   仅仅对线程调度器是一个提示 提示可以让出，调度器也有可能忽略
        *   `yield` 方法的行为是不可预测的，它仅仅提供了一种让线程主动让出CPU时间片的方式，但是否真的能够让出，以及何时让出，都取决于线程调度器的实现和系统当前的负载情况。因此，在实际应用中，应该谨慎使用 `yield` 方法。
5.  终止

    *   interrupt （设置状态）

        *   Java 中的线程中断是一种线程间的协作模式，通过设置线程的中断标志并不能直接终止该线程的执行。被中断的线程会根据中断状态自行处理。
        *   当线程 A 运行时，线程 B 可以调用线程 `interrupt()` 方法来设置线程的中断标志为 true 并立即返回。设置标志仅仅是设置标志, 线程 B 实际并没有被中断，会继续往下执行。
    *   isInterrupted方法 检查是否真的中断
    *   `boolean interrupted()` 方法： 检测当前线程是否被中断，与 isInterrupted 不同的是，该方法如果发现当前线程被中断，则会清除中断标志。
    *   为了响应中断，线程的执行代码应该这样编写：
        ```java
        public void run() {
            try {
                while (!Thread.currentThread().isInterrupted()) {
                    // 执行任务
                }
            } catch (InterruptedException e) {
                // 线程被中断时的清理代码
            } finally {
                // 线程结束前的清理代码
            }
        }

        ```

![Java线程状态变化](https://cdn.tobebetterjavaer.com/tobebetterjavaer/images/sidebar/sanfene/javathread-7.png "Java线程状态变化")

## 线程通信

*   关键字

[关键字 volatileopen in new window](https://javabetter.cn/thread/volatile.html) 用来修饰成员变量，告知程序任何对该变量的访问均需要从共享内存中获取，而对它的改变必须同步刷新回共享内存，保证所有线程对变量访问的可见性。

[关键字 synchronizedopen in new window](https://javabetter.cn/thread/synchronized-1.html) 可以修饰方法，或者以同步代码块的形式来使用，确保多个线程在同一个时刻，只能有一个线程在执行某个方法或某个代码块。

*   [ ] \*\*todo: volatile synchronized \*\*<https://javabetter.cn/thread/volatile.html>

*   等待/通知

    1.  共享对象的wait/notify
    2.  Condition类 await 和signal/signalAll

        *   通常与ReentrantLock一起使用

*   管道输入输出

    *   PipeOutputStream, PipeInputStream：面向字节
    *   ,PipeReader,PipeWriter：面向字符
    *   用于线程间传递数据，传输媒介为内存

*   Thread.join

*   ThreadLocal

    *   允许每个线程拥有自己的独立副本

        *   [ ] \*\*todo: ThreadLocal... \*\*<https://javabetter.cn/thread/ThreadLocal.html#%E5%B0%8F%E7%BB%93>

*   CountDownLatch, CyclicBarrier, Semaphore...

## synchronized

1.  Monitor

    *   monitorenter，在判断拥有同步标识 ACC\_SYNCHRONIZED 抢先进入此方法的线程会优先拥有 Monitor 的 owner ，此时计数器 +1。
    *   monitorexit，当执行完退出后，计数器 -1，归 0 后被其他进入的线程获得。

2.  可见性

    > synchronized 怎么保证可见性？

    *   线程加锁前，将清空工作内存中共享变量的值，从而使用共享变量时需要从主内存中重新读取最新的值。
    *   线程加锁后，其它线程无法获取主内存中的共享变量。
    *   线程解锁前，必须把共享变量的最新值刷新到主内存中。

3.  有序性

4.  可重入
    *   一个线程如果已经持有一个锁，可以尝试多次获取该锁而不会导致死锁
    *   monitor的计数器支持

## 锁

### 升级过程

四种状态

1.  无锁
2.  偏向锁

    *   第一个线程访问同步块，锁进入偏向模式
    *   Mark word被设置为偏向模式，存储获取它的线程id
    *   如果另一个线程尝试获取这个已被偏向的锁，JVM 会检查当前持有偏向锁的线程是否活跃。如果持有偏向锁的线程不活跃，则可以将锁重偏向至新的线程；如果持有偏向锁的线程还活跃，则需要撤销偏向锁，升级为轻量级锁。
3.  轻量级锁

    *   出现多个线程竞争
    *   没有出现线程交替执行同步块的情况
    *   CAS 没获取到说明有竞争
    *   多个线程在不同时段获取同一把锁，即不存在锁竞争的情况，也就没有线程阻塞。针对这种情况，JVM 采用轻量级锁来避免线程的阻塞与唤醒。
    *   当一个线程尝试获取轻量级锁时，它会在自己的栈帧中创建一个锁记录（Lock Record），然后尝试使用 CAS 操作将对象头的 Mark Word 替换为指向锁记录的指针。

        如果成功，该线程持有锁；如果失败，表示有其他线程竞争，锁会升级为重量级锁。
4.  重量级锁

    *   通过将对象头的Mark word指向监视器Monitor对象实现，这个对象包含了锁的持有者、锁的等待队列信息等

synchronized优化：

自旋锁

*   当线程尝试获取轻量级锁失败时，它会进行自旋，即循环检查锁是否可用，以避免立即进入阻塞状态。

    自旋的次数不是固定的，而是根据之前在同一个锁上的自旋时间和锁的状态动态调整的。

**锁粗化**：如果 JVM 检测到一系列连续的锁操作实际上是在单一线程中完成的，则会将多个锁操作合并为一个更大范围的锁操作，这可以减少锁请求的次数。

锁粗化主要针对循环内连续加锁解锁的情况进行优化。

**锁消除**：JVM 的即时编译器（[JITopen in new window](https://javabetter.cn/jvm/jit.html)）可以在运行时进行代码分析，如果发现某些锁操作不可能被多个线程同时访问，那么这些锁操作就会被完全消除。锁消除可以减少不必要的同步开销。

![三分恶面渣逆袭：锁升级简略过程](https://cdn.tobebetterjavaer.com/tobebetterjavaer/images/sidebar/sanfene/javathread-36.png "三分恶面渣逆袭：锁升级简略过程")

![三分恶面渣逆袭：synchronized 锁升级过程](https://cdn.tobebetterjavaer.com/tobebetterjavaer/images/sidebar/sanfene/javathread-37.png "三分恶面渣逆袭：synchronized 锁升级过程")

## 指令重排序

优点：

1.  提高执行效率：unit使用、缓存命中率

2.  **减少编译时间**：编译器可以花费更少的时间来生成代码，因为不需要考虑所有可能的执行顺序。

3.  提高并行度：

    指令重排序可以使得不同的处理器核心能够更有效地执行任务，提高程序的并行度。

4.  降低功耗：

    处理器可以在较短的时间内完成更多的工作，从而降低能耗。

5.  编译器优化：不改变单线程执行结果的情况下，改变无数据依赖指令的执行顺序

6.  处理器：指令级重排序。现代处理器采用了指令级并行技术（Instruction-Level Parallelism，ILP）来将多条指令重叠执行。

7.  内存系统的重排序。由于处理器使用缓存和读/写缓冲区，这使得加载和存储操作看上去可能是在乱序执行

### 原则

#### happens-before

1.  监视器原则：锁解锁happens-before后续加锁
2.  volatile： volatile域写 before 读
3.  有传递性 A - B B-C则 A-C
4.  **start()规则**：如果线程 A 执行操作 ThreadB.start()（启动线程 B），那么 A 线程的 ThreadB.start()操作 happens-before 于线程 B 中的任意操作。
5.  **join()规则**：如果线程 A 执行操作 ThreadB.join()并成功返回，那么线程 B 中的任意操作 happens-before 于线程 A 从 ThreadB.join()操作成功返回。

#### as-if-serial

单线程程序的执行结果不能被改变

为了遵守 as-if-serial 语义，编译器和处理器不会对存在数据依赖关系的操作做重排序，因为这种重排序会改变执行结果。但是，如果操作之间不存在数据依赖关系，这些操作就可能被编译器和处理器重排序。为了具体说明，请看下面计算圆面积的代码示例。

```
double pi = 3.14;   // A
double r = 1.0;   // B
double area = pi * r * r;   // C

```

A 和 C 之间存在数据依赖关系，同时 B 和 C 之间也存在数据依赖关系。因此在最终执行的指令序列中，C 不能被重排序到 A 和 B 的前面（C 排到 A 和 B 的前面，程序的结果将会被改变）。但 A 和 B 之间没有数据依赖关系，编译器和处理器可以重排序 A 和 B 之间的执行顺序。

对于多线程 通过volatile/synchronized保证as-if-serial

#### volatile

1.  可见性

    通过读/写内存屏障保证
2.  有序性

    编译器：\
    写 volatile 变量的操作之前的操作不会被编译器重排序到写操作之后。

    读 volatile 变量的操作之后的操作不会被编译器重排序到读操作之前。

\=》volatile读一定在写之后
