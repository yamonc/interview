# Java内存模型：看Java如何解决可见性和有序性问题

我们已经知道导致可见性的原因是缓存、导致有序性的原因是编译优化，那么解决这两个问题最简单直接的方法就是**禁止缓存和编译优化**，但是如果这两个解决的话，那么程序性能有所损耗。那么做合理的就是**折中方案**：按需禁用缓存和编译优化。

按需禁用缓存和编译优化只需要提供给程序员对应的方法即可。

## Java内存模型

Java内存模型规范了JVM如何提供按需禁用缓存和编译优化的方法。具体来说包括**volatile、synchronized和final**三个关键字以及六个 **Happens-Before规则**

### volatile关键字

使用volatile关键字变量告诉编译器，对于这个变量，只能从内存中读取或者写入，不能读取CPU缓存。

volatile能保证可见性，禁止指令重排，但是不能保证原子性，所以volatile是线程不安全的。

#### volatile在什么情况下保证线程安全？

1. 运算结果并不依赖变量的当前值，或者能够确保只有单一的线程修改变量的值。
2. 变量不需要与其他状态变量共同参与不变约束。

### Happens-Before规则

1. 程序的顺序性原则。这条规则是指在一个线程中，按照程序顺序，前面的操作 Happens-Before 于后续的任意操作。

2. volatile变量原则：对一个volatile变量的写操作，Happens-Before与后续对这个volatile变量的读操作。

3. 传递性原则：A happens-Before B and B Happens-Before A then A Happnes-Before C

4. 管程中锁的规则：一个锁的unLock Happens-Before于后续对这个锁的Lock。

   > 管程：同步原语，在Java中指synchronized。另外，管程中的锁在Java中是隐式实现的，在进入代码块之前会自动加锁，在代码执行结束后会自动释放锁，加锁以及解锁都是编译器帮我们实现的。TODO：关于synchronized的实现原理

5. 线程start（）规则：主线程A启动子线程B后，子线程B能够看到主线程在启动子线程B之前的操作。换句话说：如果线程 A 调用线程 B 的 start() 方法（即在线程 A 中启动线程 B），那么该 start() 操作 Happens-Before 于线程 B 中的任意操作

6. 线程join（）规则：关于线程等待，指主线程A等待子线程B完成（主线程A通过调用子线程B的join（）方法实现），当子线程B完成后（主线程A中的join（）方法返回），主线程可以看到子线程的操作。换句话说，如果在线程 A 中，调用线程 B 的 join() 并成功返回，那么线程 B 中的任意操作 Happens-Before 于该 join() 操作的返回。

### final关键字

final修饰变量的时候，告诉编译器，这个变量不变，可以优化。



