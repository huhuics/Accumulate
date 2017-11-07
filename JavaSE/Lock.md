## 多线程环境解决互斥性问题方案和原理    
[文章来源](https://tech.meituan.com/distributed-system-mutually-exclusive-idempotence-cerberus-gtis.html)    
### 解决方案    
> 基本上所有的并发模式在解决线程冲突问题的时候，都是采用序列化访问共享资源的方案。    

Java中提供了两种互斥锁`Lock`和`ReentrantLock`，这两种锁都是**可重入锁**，可重入锁可避免死锁。    
### 原理
#### ReentrantLock    
ReentrantLock主要利用`CAS+CLH`队列来实现。它支持公平锁和非公平锁。    
+ CAS: Compare And Swap，比较并交换。CAS有3个操作数：内存值V、预期值A、要修改的新值B。当且仅当预期值A和内存值V相同时，将内存值V修改为B，否则什么都不做。该操作是原子的，被广泛应用在Java底层实现中。在Java中，CAS主要是由`sun.misc.Unsafe`这个类通过JNI调用CPU底层指令实现。    
+ CLH队列：带头结点的双向非循环链表：    
![](https://github.com/huhuics/Accumulate/blob/master/image/CLH%E9%98%9F%E5%88%97.png?raw=true)    

ReentrantLock的基本实现可以概括为：先通过CAS尝试获取锁，如果此时已经有线程占据了锁，那就加入CLH队列并且挂起。当锁被释放之后，排在CLH队列队首的线程会被唤醒，然后CAS再次尝试获取锁。这个时候，如果：    
+ 非公平锁：如果同时还有另一个线程进来尝试获取，那么有可能会让这个线程抢先获取；    
+ 公平锁：如果同时还有另一个线程进来尝试获取，当它发现自己不在队首的话，就会排到队尾，由队首的线程获取到锁。    

#### Synchronized    
Java中有两种synchronized语法：synchronized语句、synchronized方法。    
+ synchronized语句：当源代码被编译成字节码的时候，会在同步块的入口位置和退出位置分别插入monitorenter和monitorexit字节码指令；    
    - 每个对象都有一个锁，也就是监视器（monitor）。当monitor被占有时就表示它被锁定。线程执行monitorenter指令时尝试获取该对象所对应的monitor的所有权。    
+ synchronized方法：在Class文件的方法表中将该方法的access_flags字段中的synchronized标识位1。





