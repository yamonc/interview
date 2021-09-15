# 快速实现一个限流器

## 信号量模型

概括为一个计数器、一个等待队列、三个方法。在信号量模型中，计数器和等待模型对外是透明的，所以只能通过信号量模型提供的三个方法来访问他们，这三个方法是：init()\down()\up().

![image-20210903100806496](https://gitee.com/yamonc/blogImage/raw/master//img/blogImage/image-20210903100806496.png)

这三个方法详细的语义具体如下所示。

- init()：设置计数器的初始值。
- down()：计数器的值减 1；如果此时计数器的值小于 0，则当前线程将被阻塞，否则当前线程可以继续执行。
- up()：计数器的值加 1；如果此时计数器的值小于或者等于 0，则唤醒等待队列中的一个线程，并将其从等待队列中移除。

这里提到的 init()、down() 和 up() 三个方法都是原子性的，并且这个原子性是由信号量模型的实现方保证的。在 Java SDK 里面，信号量模型是由 java.util.concurrent.Semaphore 实现的，Semaphore 这个类能够保证这三个方法都是原子操作。

代码：

```java
public class Semaphore {
    int count;
    Queue queue;
    Semaphore(int c){
        this.count = c;
    }
    
    void down(){
        this.count--;
        if (this.count<0){
            //将当前线程插入到等待队列
            //阻塞当前线程
        }
    }
    void up(){
        this.count++;
        if (this.count<=0){
            //移除等待队列中的某个线程T
            //唤醒线程T
        }
    }
}

```

在 Java SDK 并发包里，down() 和 up() 对应的则是 acquire() 和 release()。

## 如何使用信号量

跟互斥锁类似，在进入临界区之前执行down操作，退出临界区之前执行up操作就可以了。

```java
public class Test {
    static int count;
    static final Semaphore s=new Semaphore(1);
    static void addOne() throws InterruptedException {
        System.out.println(count);
        s.acquire();
        try {
            count+=1;
        }finally {
            s.release();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        for (int i = 0; i < 1000000; i++) {
            Test.addOne();
        }
    }
}
```

下面我们再来分析一下，信号量是如何保证互斥的。假设两个线程 T1 和 T2 同时访问 addOne() 方法，当它们同时调用 acquire() 的时候，由于 acquire() 是一个原子操作，所以只能有一个线程（假设 T1）把信号量里的计数器减为 0，另外一个线程（T2）则是将计数器减为 -1。对于线程 T1，信号量里面的计数器的值是 0，大于等于 0，所以线程 T1 会继续执行；对于线程 T2，信号量里面的计数器的值是 -1，小于 0，按照信号量模型里对 down() 操作的描述，线程 T2 将被阻塞。所以此时只有线程 T1 会进入临界区执行`count+=1；`。

当线程 T1 执行 release() 操作，也就是 up() 操作的时候，信号量里计数器的值是 -1，加 1 之后的值是 0，小于等于 0，按照信号量模型里对 up() 操作的描述，此时等待队列中的 T2 将会被唤醒。于是 T2 在 T1 执行完临界区代码之后才获得了进入临界区执行的机会，从而保证了互斥性。

## 快速实现限流器

**Semaphore 可以允许多个线程访问一个临界区**。

所谓对象池呢，指的是一次性创建出 N 个对象，之后所有的线程重复利用这 N 个对象，当然对象在被释放前，也是不允许其他线程使用的。对象池，可以用 List 保存实例对象，这个很简单。但关键是限流器的设计，这里的限流，指的是不允许多于 N 个线程同时进入临界区。那如何快速实现一个这样的限流器呢？这种场景，我立刻就想到了信号量的解决方案。

```java
public class ObjectPool<T, R> {
    final List<T> pool;
    final Semaphore sem;

    public ObjectPool(int size, T t) {
        this.pool = new Vector<T>(){};
        for (int i = 0;i<size;i++){
            pool.add(t);
        }
        this.sem = new Semaphore(size);
    }
    R exec(Function<T,R> func) throws InterruptedException {
        T t = null;
        sem.acquire();
        try {
            t = pool.remove(0);
            return func.apply(t);
        }finally {
            pool.add(t);
            sem.release();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        ObjectPool<Long, String> objectPool = new ObjectPool<Long, String>(10, 2L);
        objectPool.exec(t->{
            System.out.println(t);
            return t.toString();
        });
    }
}
```

我们用一个 List来保存对象实例，用 Semaphore 实现限流器。关键的代码是 ObjPool 里面的 exec() 方法，这个方法里面实现了限流的功能。在这个方法里面，我们首先调用 acquire() 方法（与之匹配的是在 finally 里面调用 release() 方法），假设对象池的大小是 10，信号量的计数器初始化为 10，那么前 10 个线程调用 acquire() 方法，都能继续执行，相当于通过了信号灯，而其他线程则会阻塞在 acquire() 方法上。对于通过信号灯的线程，我们为每个线程分配了一个对象 t（这个分配工作是通过 pool.remove(0) 实现的），分配完之后会执行一个回调函数 func，而函数的参数正是前面分配的对象 t ；执行完回调函数之后，它们就会释放对象（这个释放工作是通过 pool.add(t) 实现的），同时调用 release() 方法来更新信号量的计数器。如果此时信号量里计数器的值小于等于 0，那么说明有线程在等待，此时会自动唤醒等待的线程。

