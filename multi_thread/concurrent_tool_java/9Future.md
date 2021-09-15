# Future：如何用多线程实现最优的“烧水泡茶”程序？

获取任务的执行结果用Future。

## 如何获取任务执行结果？

Java 通过 ThreadPoolExecutor 提供的 3 个 submit() 方法和 1 个 FutureTask 工具类来支持获得任务执行结果的需求。

```java
// 提交 Runnable 任务
Future<?>   submit(Runnable task);
// 提交 Callable 任务
<T> Future<T>  submit(Callable<T> task);
// 提交 Runnable 任务及结果引用  
<T> Future<T>  submit(Runnable task, T result);
```

你会发现它们的返回值都是 Future 接口，Future 接口有 5 个方法，我都列在下面了，它们分别是**取消任务的方法 cancel()、判断任务是否已取消的方法 isCancelled()、判断任务是否已结束的方法 isDone()\**以及\**2 个获得任务执行结果的 get() 和 get(timeout, unit)**，其中最后一个 get(timeout, unit) 支持超时机制。通过 Future 接口的这 5 个方法你会发现，我们提交的任务不但能够获取任务执行结果，还可以取消任务。不过需要注意的是：这两个 get() 方法都是阻塞式的，如果被调用的时候，任务还没有执行完，那么调用 get() 方法的线程会阻塞，直到任务执行完才会被唤醒。

```java
// 取消任务
boolean cancel(boolean mayInterruptIfRunning);
// 判断任务是否已取消  
boolean isCancelled();
// 判断任务是否已结束
boolean isDone();
// 获得任务执行结果
get();
// 获得任务执行结果，支持超时
get(long timeout, TimeUnit unit);

```

这 3 个 submit() 方法之间的区别在于方法参数不同，下面我们简要介绍一下。

1. 提交 Runnable 任务 `submit(Runnable task)` ：这个方法的参数是一个 Runnable 接口，Runnable 接口的 run() 方法是没有返回值的，所以 `submit(Runnable task)` 这个方法返回的 Future 仅可以用来断言任务已经结束了，类似于 Thread.join()。
2. 提交 Callable 任务 `submit(Callable<T> task)`：这个方法的参数是一个 Callable 接口，它只有一个 call() 方法，并且这个方法是有返回值的，所以这个方法返回的 Future 对象可以通过调用其 get() 方法来获取任务的执行结果。
3. 提交 Runnable 任务及结果引用 `submit(Runnable task, T result)`：这个方法很有意思，假设这个方法返回的 Future 对象是 f，f.get() 的返回值就是传给 submit() 方法的参数 result。这个方法该怎么用呢？下面这段示例代码展示了它的经典用法。需要你注意的是 Runnable 接口的实现类 Task 声明了一个有参构造函数 `Task(Result r)` ，创建 Task 对象的时候传入了 result 对象，这样就能在类 Task 的 run() 方法中对 result 进行各种操作了。result 相当于主线程和子线程之间的桥梁，通过它主子线程可以共享数据。

下面我们再来介绍 FutureTask 工具类。前面我们提到的 Future 是一个接口，而 FutureTask 是一个实实在在的工具类，这个工具类有两个构造函数，它们的参数和前面介绍的 submit() 方法类似，所以这里我就不再赘述了。

```java
FutureTask(Callable<V> callable);
FutureTask(Runnable runnable, V result);

```

那如何使用 FutureTask 呢？其实很简单，FutureTask 实现了 Runnable 和 Future 接口，由于实现了 Runnable 接口，所以可以将 FutureTask 对象作为任务提交给 ThreadPoolExecutor 去执行，也可以直接被 Thread 执行；又因为实现了 Future 接口，所以也能用来获得任务的执行结果。下面的示例代码是将 FutureTask 对象提交给 ThreadPoolExecutor 去执行。

```java
// 创建 FutureTask
FutureTask<Integer> futureTask = new FutureTask<>(()-> 1+2);
// 创建线程池
ExecutorService es =  Executors.newCachedThreadPool();
// 提交 FutureTask 
es.submit(futureTask);
// 获取计算结果
Integer result = futureTask.get();
```

FutureTask 对象直接被 Thread 执行的示例代码如下所示。相信你已经发现了，利用 FutureTask 对象可以很容易获取子线程的执行结果。

```java
// 创建 FutureTask
FutureTask<Integer> futureTask = new FutureTask<>(()-> 1+2);
// 创建并启动线程
Thread T1 = new Thread(futureTask);
T1.start();
// 获取计算结果
Integer result = futureTask.get();
```

