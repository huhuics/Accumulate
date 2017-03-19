# MultiThreadsDemo
 + 调用`thread().interrupt()`是中断一个线程。被中断的线程运行时如果在执行可中断的阻塞方法时，都会检测该线程是否被中断了，如果中断了则抛出`InterruptedException`，从而停止当前阻塞方法。捕获该异常以后，如果希望停止该线程应该这样做：
```java
Thread.currentThread().interrupt(); // Here!
throw new RuntimeException(ex);
```  
如果没有将异常封装成`RuntimeException`，该线程是不会停止的
 + 如果初始化`ThreadLocal`变量使用如下方式，则对所有线程该变量都是可见的：
 ```java
 private ThreadLocal<Integer> threadLocal = new ThreadLocal<Integer>() {
                                                     /**
                                                      * 这种方法初始化ThreadLocal变量对所有线程都可见
                                                      */
                                                     @Override
                                                     protected Integer initialValue() {
                                                         return 99;
                                                     }

                                                 };
 ```
  而使用`set`方式设置`ThreadLocal`变量的值，则只对当前线程可见
  + `volatile`的作用不是使变量具有原子性，而是使变量具有可见性，即变量被某一线程修改后，其它线程可以立刻感知到。被`volatile`修饰的变量，编译器与运行时都会注意到这个变量的共享的，因此**不会将该变量上的操作与其他内存操作一起重排序**。`volatile`变量不会被缓存在寄存器或者其它处理器看不见的地方，因此读取`volatile`类型的变量时总会返回最新写入的值。
  + `volatile`变量是一种更轻量级的同步机制，因为在使用这些变量时不会发生上下文切换或者线程调度等操作。然后`volatile`也存在一些局限：虽然他们提供了可见性保证，但不能用于构建原子的复合操作。
  + `join`方法是等待当前线程执行完
  + `yield`方法不是暂停当前线程，而是让出资源使用权，如果此时没有其他线程，则该线程继续执行
  + `AtomicInteger`变量具有原子性，所谓原子性是指对该变量的操作要么成功，要么失败，且在线程安全，即同一时间只允许一个线程操作该变量
  + `synchronized`的使用：
  ```java
  public synchronized void method() { // blocks "this" from here.... 
    ...
  } // to here

  public void method() { 
      synchronized( this ) { // blocks "this" from here .... 
          ....
      }  // to here...
  }
  ```
  以上两种方式实际是没有区别的。使用这种方式加锁，将锁住整个对象，线程A获取锁以后，线程B将无法访问这个对象的任何被`synchronized`修饰的方法，但可以访问其它非同步方法，即没有被`synchronized`修饰的方法。但是下面这种加锁方式比上面的方式效率高一倍左右：
  + 使用其它对象锁
  ```java
  // Using specific locks
  Object inputLock = new Object();

  private void someInputRelatedWork() {
      synchronize(inputLock) { 
          ... 
      } 
  }
  ```
  + 一个线程池中，既可以多个线程同时执行一个任务，也可以多个线程执行多个不同的任务
  + `awaitTermination`是让主线程等待线程池的任务执行，如果等待的过程中发生了请求关闭、超时或者当前线程中断，则主线程继续往下执行，而线程池中的任务继续执行，直到任务结束
  + **可重入锁**指的是在一个线程中可以多次获取同一把锁，比如：一个线程在执行一个带锁的方法，该方法又调用了另一个需要向同锁的方法，该线程可以直接执行调用的方法，而不需要重新获得锁。可重入锁的最大作用是**避免死锁**。`ReentrantLock`和`synchronized`都是可重入锁
  + `semaphore.release(n)`如果一个线程没有获取任何permit，则调用该方法将增加n个permits。如果一个线程申请了x个permit，但释放了y个（y>x），将增加y-x个permits
  + `ExecutorService.invokeAll`将提交一组任务，返回时间取决于任务中最长的那个
  + `ExecutorService.invokeAny`将提交一组任务，那个任务先完成（未抛出异常）则返回其结果，一旦正常或异常返回后，则取消尚未完成的任务
  + 在Java中，线程不允许抛出`checked exception`，也就是说各个线程自己要把自己的`checked exception`处理掉。例如，在`Runnable.run()`签名中没有申明`throws Exception`。但是线程依然可能抛出`unchecked exception`，当线程抛出此类异常时线程就会终结，而对于主线程和其它线程完全不受影响，且完全感知不到某个线程抛出的异常（也就是说无法catch到这个异常）。JVM的这种设计源自一种理念：线程是独立执行的代码片段，线程的问题应该由线程自己来解决，而不委托到外部。基于这种理念，线程方法的异常（无论是checked 还是unchecked exception），都应该在线程代码边界（run方法内）进行try catch并处理掉。换句话说，我们不能捕获从线程中逃逸的异常。
  + 如果需要捕获线程的unchecked exception应该怎么办？可以自定义类实现`Thread.UncaughtExceptionHandler`接口，Thread允许我们在每一个Thread对象上添加一个异常处理器`UncaughtExceptionHandler`，通过`thread.setUncaughtExceptionHandler`方法设置。
  + 而在线程池中比较特殊。默认情况下，线程池java.util.concurrent.ThreadPoolExecutor会catch住所有异常，如果使用submit(Runnable)或使用submit(Callable)获取其结果(java.util.concurrent.Future.get())会抛出ExecutionException，该异常即是Runnable或者Callable抛出的异常。也就是说，当我们向线程池提交了任务时，如果不理会任务结果（Future.get()），那么此异常将被线程吞掉。
  + 如果需要获取线程池在运行时抛出的异常，有三种方法：
    1. 通过Future.get获取
    2. 通过继承并覆盖ThreadPoolExecutor.afterExecute(Runnable r, Throwable t)获取
    3. 通过实现ThreadFactory接口，覆盖newThread方法，并在该方法中通过setUncaughtExceptionHandler设置自定义的Handler
  + 通常可用以下方式来提升程序可伸缩性
    1. 减少锁的持有时间
    2. 降低锁的粒度。例如将锁分段
    3. 采用非独占的锁或非阻塞锁来代替独占锁。例如使用原子变量
  + 内置锁`synchronized`相比于`ReentrantLock`有一些局限性，如无法中断一个正在等待获取锁的线程，无法实现非阻塞结构的加锁规则，`ReentrantLock`提供可定时的、可轮询的与可中断的锁获取操作，公平队列，以及非块结构的锁。而`ReentrantLock`不能完全取代`synchronized`的原因是，如果不释放锁，`ReentrantLock`相当于定时炸弹，当程序执行控制离开被保护的代码块时，不会自动清除锁。
  + 在JDK6以后，`synchronized`与`ReentrantLock`二者性能已经非常接近。显示的`Lock`提供了一些扩展功能，更具灵活性，但`ReentrantLock`不能完全替代`synchronized`，只有在`synchronized`无法满足需求时，才应该使用它。
  + `ReentrantLock`可以创建一个非公平的锁（默认）和公平锁，公平锁上，线程将按照它们发出请求的顺序来获得锁，但在非公平锁上，则允许“插队”。公平锁的性能比非公平锁要差约两个数量级，一个原因是在恢复一个被挂起的线程与该线程真正开始工作之间存在着严重的时延，因此不必要的话，不要为公平性付出代价。
  + 对于在多处理器系统上被频繁读取的数据结构，`ReadWriteLock`能够提高性能，而在其它情况下，读写锁的性能比独占锁性能要差一些，因为他们复杂性更高。
  + 原子变量采用的算法是CPU底层的原子机器指令（例如比较并交换指令），代替锁来确保数据在并发访问中的一致性。原子变量也是一种“更好的volatile类型变量”，提供了与volatile类型变量相同的内存语义，此外还支持原子的更新操作，从而更适合用于实现计数器、序列发生器和统计数据收集等。
  + 在中低程度的竞争下，原子变量能提供更高的可伸缩性，性能更高；而在高强度的竞争下，锁能够更有效地避免竞争，性能更高。
  + 非阻塞算法通过底层的并发原语（例如比较并交换而不是锁）来维持线程的安全性。这些底层的原语通过原子变量类向外公开，这些类也用做一种“更好的volatile变量”，从而为整数和对象引用提供原子的更新操作。
  + `final`域如果在类构造方法方法中初始化，那么不是用同步也可以保证是安全的，但非`final`则不行。
  + 双重检查加锁(DCL)这种使用方式已经被广泛地废弃，原因是当没有同步的情况下读取一个共享对象时，可能发生的最糟糕事情是线程可能看到引用的当前值，但对象的状态值却是失效的。更底层原因是当缺少`Happens-Before`关系时，就可能出现重指令排序问题。
  + `Executors.newCachedThreadPool`使用的是`SynchronousQueue`来避免任务排队，`SynchronousQueue`不是一个真正的队列，里面没有元素，它是一种在线程之间进行移交的机制。要将一个元素放入`SynchronousQueue`中，必须有另一个线程正在等待接受这个元素。如果没有线程正在等待，并且线程池的当前大小小于最大值，那么线程池将创建一个新的线程，否则根据饱和策略，这个任务将被拒绝。只有当线程池线程数量是无界或者可以拒绝任务时，`SynchronousQueue`才有实用价值。
  + `ExecutorService.invokeAll`会等待所有任务执行完成才返回，因此主线程运行到这里将会被阻塞。`threadPool.shutdown()`是在`threadPool.awaitTermination`**之前**调用，shutdown目的是使得线程池不再接受任务。
  + ConcurrentHashMap
  ```java
  hash >>> segmentShift) & segmentMask//定位Segment所使用的hash算法
int index = hash & (tab.length - 1);// 定位HashEntry所使用的hash算法
  ```
