# Lock和Condition：隐藏在并发包中的管程

在并发编程领域中，有两个核心问题：一个是互斥，即同一时刻只允许一个线程访问共享资源；另外一个是同步，即线程之间如何通信、协作。

**Java SDK 并发包通过 Lock 和 Condition 两个接口来实现管程，其中 Lock 用于解决互斥问题，Condition 用于解决同步问题**。

Java 语言本身提供的 synchronized 也是管程的一种实现，既然 Java 从语言层面已经实现了管程了，那为什么还要在 SDK 里提供另外一种实现呢？

## 再造管程的理由

到这里，关于这个问题，你是否能够想出一条理由来呢？如果你细心的话，也许能想到一点。那就是我们前面在介绍[死锁问题](https://time.geekbang.org/column/article/85001)的时候，提出了一个**破坏不可抢占条件**方案，但是这个方案 synchronized 没有办法解决。原因是 synchronized 申请资源的时候，如果申请不到，线程直接进入阻塞状态了，而线程进入阻塞状态，啥都干不了，也释放不了线程已经占有的资源。但我们希望的是：

> 对于“不可抢占”这个条件，占用部分资源的线程进一步申请其他资源时，如果申请不到，可以主动释放它占有的资源，这样不可抢占这个条件就破坏掉了。

那么怎么利用一把互斥锁解决这个问题？

1. synchronized 的问题是，持有锁 A 后，如果尝试获取锁 B 失败，那么线程就进入阻塞状态，一旦发生死锁，就没有任何机会来唤醒阻塞的线程。但如果阻塞状态的线程能够响应中断信号，也就是说当我们给阻塞的线程发送中断信号的时候，能够唤醒它，那它就有机会释放曾经持有的锁 A。这样就破坏了不可抢占条件了。
2. **支持超时**。如果线程在一段时间之内没有获取到锁，不是进入阻塞状态，而是返回一个错误，那这个线程也有机会释放曾经持有的锁。这样也能破坏不可抢占条件。
3. **非阻塞地获取锁**。如果尝试获取锁失败，并不进入阻塞状态，而是直接返回，那这个线程也有机会释放曾经持有的锁。这样也能破坏不可抢占条件。

这三种方案可以全面弥补 synchronized 的问题。到这里相信你应该也能理解了，这三个方案就是“重复造轮子”的主要原因，体现在 API 上，就是 Lock 接口的三个方法。详情如下：

```java
// 支持中断的 API
void lockInterruptibly() 
  throws InterruptedException;
// 支持超时的 API
boolean tryLock(long time, TimeUnit unit) 
  throws InterruptedException;
// 支持非阻塞获取锁的 API
boolean tryLock();
```

## 如何保证可见性

线程 T1 对 value 进行了 +=1 操作，那后续的线程 T2 能够看到 value 的正确结果吗？

```java
public class X {
    private final Lock rtl = new ReentrantLock();
    int value;
    public void addOne(){
        rtl.lock();
        try{
            value+=1;
        }finally {
            rtl.unlock();
        }
    }
}
```

答案必须是肯定的。**Java SDK 里面锁**的实现非常复杂，这里我就不展开细说了，但是原理还是需要简单介绍一下：它是**利用了 volatile 相关的 Happens-Before 规则**。Java SDK 里面的 ReentrantLock，内部持有一个 volatile 的成员变量 state，获取锁的时候，会读写 state 的值；解锁的时候，也会读写 state 的值（简化后的代码如下面所示）。也就是说，在执行 value+=1 之前，程序先读写了一次 volatile 变量 state，在执行 value+=1 之后，又读写了一次 volatile 变量 state。根据相关的 Happens-Before 规则：

1. **顺序性规则**：对于线程 T1，value+=1 Happens-Before 释放锁的操作 unlock()；
2. **volatile 变量规则**：由于 state = 1 会先读取 state，所以线程 T1 的 unlock() 操作 Happens-Before 线程 T2 的 lock() 操作；（先读后写）
3. **传递性规则**：线程 T1 的 value+=1 Happens-Before 线程 T2 的 lock() 操作。

## 可重入锁

**所谓可重入锁，顾名思义，指的是线程可以重复获取同一把锁**。例如下面代码中，当线程 T1 执行到 ① 处时，已经获取到了锁 rtl ，当在 ① 处调用 get() 方法时，会在 ② 再次对锁 rtl 执行加锁操作。此时，如果锁 rtl 是可重入的，那么线程 T1 可以再次加锁成功；如果锁 rtl 是不可重入的，那么线程 T1 此时会被阻塞。

除了可重入锁，可能你还听说过可重入函数，可重入函数怎么理解呢？指的是线程可以重复调用？显然不是，所谓**可重入函数，指的是多个线程可以同时调用该函数**，每个线程都能得到正确结果；同时在一个线程内支持线程切换，无论被切换多少次，结果都是正确的。多线程可以同时执行，还支持线程切换，这意味着什么呢？线程安全啊。所以，可重入函数是线程安全的。

```java
class X {
  private final Lock rtl =
  new ReentrantLock();
  int value;
  public int get() {
    // 获取锁
    rtl.lock();         ②
    try {
      return value;
    } finally {
      // 保证锁能释放
      rtl.unlock();
    }
  }
  public void addOne() {
    // 获取锁
    rtl.lock();  
    try {
      value = 1 + get(); ①
    } finally {
      // 保证锁能释放
      rtl.unlock();
    }
  }
}
```

## 公平锁和非公平锁

在使用 ReentrantLock 的时候，你会发现 ReentrantLock 这个类有两个构造函数，一个是无参构造函数，一个是传入 fair 参数的构造函数。fair 参数代表的是锁的公平策略，如果传入 true 就表示需要构造一个公平锁，反之则表示要构造一个非公平锁。

```
// 无参构造函数：默认非公平锁
public ReentrantLock() {
    sync = new NonfairSync();
}
// 根据公平策略参数创建锁
public ReentrantLock(boolean fair){
    sync = fair ? new FairSync() 
                : new NonfairSync();
}
```

我们介绍过入口等待队列，锁都对应着一个等待队列，如果一个线程没有获得锁，就会进入等待队列，当有线程释放锁的时候，就需要从等待队列中唤醒一个等待的线程。如果是公平锁，唤醒的策略就是谁等待的时间长，就唤醒谁，很公平；如果是非公平锁，则不提供这个公平保证，有可能等待时间短的线程反而先被唤醒。

## 锁的最佳实践

1. 永远只在更新对象的成员变量时加锁
2. 永远只在访问可变的成员变量时加锁
3. 永远不在调用其他对象的方法时加锁

这三条规则，前两条估计你一定会认同，最后一条你可能会觉得过于严苛。但是我还是倾向于你去遵守，因为调用其他对象的方法，实在是太不安全了，也许“其他”方法里面有线程 sleep() 的调用，也可能会有奇慢无比的 I/O 操作，这些都会严重影响性能。更可怕的是，“其他”类的方法可能也会加锁，然后双重加锁就可能导致死锁。

----

## Dobbo如何使用管程实现异步转同步

**Condition 实现了管程模型里面的条件变量**。

一个阻塞队列，需要两个条件变量，一个是队列不空（空队列不允许出队），另一个是队列不满（队列已满不允许入队），这个例子我们前面在介绍[管程](https://time.geekbang.org/column/article/86089)的时候详细说过，这里就不再赘述。相关的代码，我这里重新列了出来，你可以温故知新一下。

```java
public class BlockedQueue<T>{
  final Lock lock =
    new ReentrantLock();
  // 条件变量：队列不满  
  final Condition notFull =
    lock.newCondition();
  // 条件变量：队列不空  
  final Condition notEmpty =
    lock.newCondition();
 
  // 入队
  void enq(T x) {
    lock.lock();
    try {
      while (队列已满){
        // 等待队列不满
        notFull.await();
      }  
      // 省略入队操作...
      // 入队后, 通知可出队
      notEmpty.signal();
    }finally {
      lock.unlock();
    }
  }
  // 出队
  void deq(){
    lock.lock();
    try {
      while (队列已空){
        // 等待队列不空
        notEmpty.await();
      }  
      // 省略出队操作...
      // 出队后，通知可入队
      notFull.signal();
    }finally {
      lock.unlock();
    }  
  }
}
```

不过，这里你需要注意，Lock 和 Condition 实现的管程，**线程等待和通知需要调用 await()、signal()、signalAll()**，它们的语义和 wait()、notify()、notifyAll() 是相同的。但是不一样的是，Lock&Condition 实现的管程里只能使用前面的 await()、signal()、signalAll()，而后面的 wait()、notify()、notifyAll() 只有在 synchronized 实现的管程里才能使用。如果一不小心在 Lock&Condition 实现的管程里调用了 wait()、notify()、notifyAll()，那程序可就彻底玩儿完了。

## 同步与异步

**通俗点来讲就是调用方是否需要等待结果，如果需要等待结果，就是同步；如果不需要等待结果，就是异步**。

同步，是 Java 代码默认的处理方式。如果你想让你的程序支持异步，可以通过下面两种方式来实现：

1. 调用方创建一个子线程，在子线程中执行方法调用，这种调用我们称为异步调用；
2. 方法实现的时候，创建一个新的线程执行主要逻辑，主线程直接 return，这种方法我们一般称为异步方法。

## Dubbo源码分析

其实在编程领域，异步的场景还是挺多的，比如 TCP 协议本身就是异步的，我们工作中经常用到的 RPC 调用，**在 TCP 协议层面，发送完 RPC 请求后，线程是不会等待 RPC 的响应结果的**。可能你会觉得奇怪，平时工作中的 RPC 调用大多数都是同步的啊？这是怎么回事呢？

其实很简单，一定是有人帮你做了异步转同步的事情。例如目前知名的 RPC 框架 Dubbo 就给我们做了异步转同步的事情，那它是怎么做的呢？下面我们就来分析一下 Dubbo 的相关源码。

对于下面一个简单的 RPC 调用，默认情况下 sayHello() 方法，是个同步方法，也就是说，执行 service.sayHello(“dubbo”) 的时候，线程会停下来等结果。

```java
DemoService service = 初始化部分省略
String message = 
  service.sayHello("dubbo");
System.out.println(message);
```

到这里时候，线程已经阻塞了，线程状态是TIMED_WATING。本来发送请求是异步的，但是调用线程却阻塞，说明Dubbo帮我们做了异步转同步的事情。

Dubbo 异步转同步的功能应该是通过 DefaultFuture 这个类实现的。

