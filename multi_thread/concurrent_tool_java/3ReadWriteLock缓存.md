# ReadWriteLock：如何快速实现一个完备的缓存？

ReadWriteLock用于读多写少的情景下。比如为了性能优化，我们经常使用缓存，例如缓存元数据、缓存基础数据等，典型的读多写少应用场景。

## 什么是读写锁？

读写锁，并不是 Java 语言特有的，而是一个广为使用的通用技术，所有的读写锁都遵守以下三条基本原则：

1. 允许多个线程同时读共享变量；
2. 只允许一个线程写共享变量；
3. 如果一个写线程正在执行写操作，此时禁止读线程读共享变量。

读写锁与互斥锁的一个重要区别就是**读写锁允许多个线程同时读共享变量**，而互斥锁是不允许的，这是读写锁在读多写少场景下性能优于互斥锁的关键。但**读写锁的写操作是互斥的**，当一个线程在写共享变量的时候，是不允许其他线程执行写操作和读操作。

## 快速实现一个缓存

Cache 这个工具类，我们提供了两个方法，一个是读缓存方法 get()，另一个是写缓存方法 put()。读缓存需要用到读锁，读锁的使用和前面我们介绍的 Lock 的使用是相同的，都是 try{}finally{}这个编程范式。写缓存则需要用到写锁，写锁的使用和读锁是类似的。这样看来，读写锁的使用还是非常简单的。

```java
public class CacheTest<K, V> {
    final Map<K, V> m = new HashMap<>();
    final ReadWriteLock rwl = new ReentrantReadWriteLock();
    final Lock r = rwl.readLock();
    final Lock w = rwl.writeLock();


    V get(K key){
        r.lock();
        try{
            return m.get(key);
        }finally {
            r.unlock();
        }
    }

    V put(K key, V v){
        w.lock();
        try {
            return m.put(key, v);
        }finally {
            w.unlock();
        }
    }
}
```

如果使用过缓存的话，使用缓存首先应该解决缓存数据的初始化问题，缓存数据的初始化，可以采用一次性加载的方式，也可以采用按需加载的方式。

如果源头数据的数据量不大，就可以采用一次性加载的方法。

![image-20210903152358502](https://gitee.com/yamonc/blogImage/raw/master//img/blogImage/image-20210903152358502.png)

如果数据源比较多，按需加载，按需加载也叫懒加载，指的是只有当应用查询缓存的时候，并且数据不在缓存中的时候，才触发加载源头相关数据进缓存的操作。

![image-20210903152506347](https://gitee.com/yamonc/blogImage/raw/master//img/blogImage/image-20210903152506347.png)

## 实现缓存按需加载

```java
  final Map<K, V> m = new HashMap<>();
    final ReadWriteLock rwl = new ReentrantReadWriteLock();
    final Lock r = rwl.readLock();
    final Lock w = rwl.writeLock();


    V get(K key){
        V v = null;
        r.lock();
        try{
            v = m.get(key);
        }finally {
            r.unlock();
        }
        //缓存命中
        if (v!=null){
            return v;
        }
        //缓存没有命中，不存在，查询数据库
        w.lock();
        try{
            //再次验证
            //其他线程可能已经查询过数据库了
            v = m.get(key);
            if (v == null){
                //查询数据库
                //v=
                m.put(key, v);
                
            }
        }finally {
            w.unlock();
        }
        return v;
    }
```

## 读写锁的升级和降级

上面按需加载的示例代码中，在①处获取读锁，在③处释放读锁，那是否可以在②处的下面增加验证缓存并更新缓存的逻辑呢？详细的代码如下。

```
// 读缓存
r.lock();         ①
try {
  v = m.get(key); ②
  if (v == null) {
    w.lock();
    try {
      // 再次验证并更新缓存
      // 省略详细代码
    } finally{
      w.unlock();
    }
  }
} finally{
  r.unlock();     ③
}
```

这样看上去好像是没有问题的，先是获取读锁，然后再升级为写锁，对此还有个专业的名字，叫**锁的升级**。可惜 ReadWriteLock 并不支持这种升级。在上面的代码示例中，读锁还没有释放，此时获取写锁，会导致写锁永久等待，最终导致相关线程都被阻塞，永远也没有机会被唤醒。锁的升级是不允许的，这个你一定要注意。

不过，虽然锁的升级是不允许的，但是锁的降级却是允许的。