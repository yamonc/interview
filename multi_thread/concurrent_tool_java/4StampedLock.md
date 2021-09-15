# StampedLock:有没有比读写锁更快的锁？

Java 在 1.8 这个版本里，提供了一种叫 StampedLock 的锁，它的性能就比读写锁还要好。

## StampedLock 支持的三种锁模式

ReadWriteLock 支持两种模式：一种是读锁，一种是写锁。而 StampedLock 支持三种模式，分别是：**写锁**、**悲观读锁**和**乐观读**。其中，写锁、悲观读锁的语义和 ReadWriteLock 的写锁、读锁的语义非常类似，允许多个线程同时获取悲观读锁，但是只允许一个线程获取写锁，写锁和悲观读锁是互斥的。不同的是：StampedLock 里的写锁和悲观读锁加锁成功之后，都会返回一个 stamp；然后解锁的时候，需要传入这个 stamp。相关的示例代码如下。

```java
public class Stamp {
    final StampedLock s1 = new StampedLock();
    long stamp = s1.readLock();
    public void read(){
        try{
            //业务逻辑
        }finally {
            s1.unlockRead(stamp);
        }
    }
    public void get(){
        long stamp = s1.writeLock();
        try{
            //业务逻辑
        }finally {
            s1.unlockWrite(stamp);
        }
    }
}
```

ReadWriteLock：支持多个线程同时读，但是当多个线程同时读的时候，所有的写操作都会被阻塞；

StampedLock：提供了乐观读，是允许一个线程获取写锁，也就是说不是所有的写操作都被阻塞，乐观读这个操作是无锁的。

文中下面这段代码是出自 Java SDK 官方示例，并略做了修改。在 distanceFromOrigin() 这个方法中，首先通过调用 tryOptimisticRead() 获取了一个 stamp，这里的 tryOptimisticRead() 就是我们前面提到的乐观读。之后将共享变量 x 和 y 读入方法的局部变量中，不过需要注意的是，由于 tryOptimisticRead() 是无锁的，所以共享变量 x 和 y 读入方法局部变量时，x 和 y 有可能被其他线程修改了。因此最后读完之后，还需要再次验证一下是否存在写操作，这个验证操作是通过调用 validate(stamp) 来实现的。

```
class Point {
  private int x, y;
  final StampedLock sl = 
    new StampedLock();
  // 计算到原点的距离  
  int distanceFromOrigin() {
    // 乐观读
    long stamp = 
      sl.tryOptimisticRead();
    // 读入局部变量，
    // 读的过程数据可能被修改
    int curX = x, curY = y;
    // 判断执行读操作期间，
    // 是否存在写操作，如果存在，
    // 则 sl.validate 返回 false
    if (!sl.validate(stamp)){
      // 升级为悲观读锁
      stamp = sl.readLock();
      try {
        curX = x;
        curY = y;
      } finally {
        // 释放悲观读锁
        sl.unlockRead(stamp);
      }
    }
    return Math.sqrt(
      curX * curX + curY * curY);
  }
}
```

在上面这个代码示例中，如果执行乐观读操作的期间，存在写操作，会把乐观读升级为悲观读锁。这个做法挺合理的，否则你就需要在一个循环里反复执行乐观读，直到执行乐观读操作的期间没有写操作（只有这样才能保证 x 和 y 的正确性和一致性），而循环读会浪费大量的 CPU。升级为悲观读锁，代码简练且不易出错，建议你在具体实践时也采用这样的方法。

## 进一步理解乐观读

数据库中的乐观锁：添加version字段，更新的时候，先查询出来version的值，然后update操作，将version的值+1，如果这期间有人更改过这条数据，则version的变化值肯定大于1.

## StampedLock使用注意事项

StampedLock适合读多写少的场景，并且在这些场景下性能很好，简单的场景下可以代替ReadWriteLock，但是StampedLock的功能仅仅是ReadWriteLock的子集：

StampedLock没有增加Reentrant，证明StampedLock不支持重入。

另外StampedLock的悲观读锁、写锁都不支持条件变量。

如果线程阻塞在 StampedLock 的 readLock() 或者 writeLock() 上时，此时调用该阻塞线程的 interrupt() 方法，会导致 CPU 飙升。

**使用 StampedLock 一定不要调用中断操作，如果需要支持中断功能，一定使用可中断的悲观读锁 readLockInterruptibly() 和写锁 writeLockInterruptibly()**。

----

## 最佳示例：

StampedLock 读模板：

```
final StampedLock sl = 
  new StampedLock();
 
// 乐观读
long stamp = 
  sl.tryOptimisticRead();
// 读入方法局部变量
......
// 校验 stamp
if (!sl.validate(stamp)){
  // 升级为悲观读锁
  stamp = sl.readLock();
  try {
    // 读入方法局部变量
    .....
  } finally {
    // 释放悲观读锁
    sl.unlockRead(stamp);
  }
}
// 使用方法局部变量执行业务操作
......
```

StampedLock 写模板：

```
long stamp = sl.writeLock();
try {
  // 写共享变量
  ......
} finally {
  sl.unlockWrite(stamp);
}
```