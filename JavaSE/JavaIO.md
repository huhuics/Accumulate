# Java I/O学习
+ **流**代表任何有能力产生数据的数据源对象或者是有能力接收数据的接收端对象。**流**屏蔽了实际I/O设备处理数据的细节
+ `FilterInputStream`和`FilterOutputStream`是用来提供装饰器类接口以控制特定输入流(`InputStream`)和输出流(`OutputStream`)的两个类，这两个类是装饰器的必要条件，以便能为所有正在被修饰的对象提供通用接口
+ `InputStreamReader`可以把`InputStream`转换为`Reader`，而`OutputStreamWriter`可以把`OutputStream`转换为`Writer`
+ `BufferedInputStream.available()`方法可以用来产生输入流的估计字节数
+ `Java NIO`速度的提升来自于所使用的结构更接近于操作系统执行`I/O`，**通道**和**缓冲器**。可以把它想象成一个煤矿，通道是包含煤层（数据）的矿藏，而缓冲器则是派送到矿藏的卡车。卡车载满煤炭而归，我们再从卡车上获得煤炭。也就是说我们没有直接和通道交互，我们只是和缓冲器交互，并把缓冲器派到通道。通道要么从缓冲器获得数据，要么向缓冲器发送数据。
+ `ByteBuffer.rewind()`重绕此缓冲区，将当前位置设置为0；`ByteBuffer.flip()`首先将缓冲区的大小限制设置为当前位置，然后再将当前位置设置为0，`flip`用于准备从缓冲区读取已经写入的数据。The `flip()` method switches a Buffer from writing mode to reading mode. Calling flip() sets the position back to 0, and sets the limit to where position just was.
+ Java的对象序列化将那些实现了`Serializable`接口的对象转换长一个字节序列，并能够在以后将这个字节序列完全恢复为原来的对象。这一过程甚至可以通过网络进行；这意味着序列化机制能自动弥补不同操作系统之间的差异
+ 介绍NIO——NIO引入了四个关键的抽象数据类型
    - `Buffer`: 本质上是内存块，可以用于读写数据
    - `Charset`: 提供Unicode字符串映射到字节序列以及逆映射的操作
    - `Channel`: 包含socket, file和pipe三种管道。代表一个到实体（如一个硬件设备、一个文件、一个网络套接字或者一个能够执行一个或者多个不同I/O操作的程序组件）的开放连接，如读操作和写操作
    - `Selector`: 允许一个线程控制多个Channel    
    ![Channel](http://tutorials.jenkov.com/images/java-nio/overview-channels-buffers.png)  
    ![Selector](http://tutorials.jenkov.com/images/java-nio/overview-selectors.png)
+ Nio和IO的主要区别
    - 面向流与面向缓冲。IO是面向流的，NIO是面向缓冲的。IO面向流意味着每次从流中读一个或多个字节，甚至读取所有字节，它们没有被缓存在任何地方。此外，它不能前后移动流中的数据。NIO数据读取到一个稍后处理的缓冲区，需要时可在缓冲区前后移动。这就增加了处理过程中的灵活性
    - 阻塞与非阻塞。IO的各种流是阻塞的，这意味着当一个线程调用`read()`或`write()`时，该线程被阻塞，直到有一些数据被读取，或数据完全写入，改线程在此期间不能再做任何事情了。NIO是非阻塞模式，一个线程从某通道发送请求读取数据，但是它仅能得到目前可用的数据，如果目前没有数据可用，就什么都不会获取，直到数据可以读取之前，该线程可以继续做其它的事情，而不是保持阻塞。非阻塞写也是如此，一个线程请求写入一些数据到某通道，但不需要等待它完全写入，这个线程同时可以去做别的事情。线程通过将非阻塞IO的空闲时间用于在其它通道上执行IO操作，所以一个单独的线程可以管理多个输入和输出通道(channel)。NIO可只使用一个（或几个）单独线程管理多个通道（网络连接或文件），但付出的代价是解析数据可能会比从一个阻塞流中读取数据更复杂
 + NIO优点
     - 如果需要管理同时打开的成千上万个连接，这些连接每次只是发送少量的数据，例如聊天服务器，使用NIO实现可能是一个优势
     - 如果需要维持许多打开的连接到其它计算机上，例如P2P网络中，使用一个单独的线程来管理所有的出站连接，NIO可能是一个优势
 + IO优点
     - 如果有少量的连接使用非常高的宽带，一次发送大量的数据，IO实现可能更加合适
 + BIO示意图
![BIO](http://img.blog.csdn.net/20150507161118213?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHVodWlfY3M=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
 + NIO示意图
![NIO](http://img.blog.csdn.net/20150507165828514?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHVodWlfY3M=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
 + `FileChannel`是连接文件的；`SocketChannel`是连接TCP网络socket；`ServerSocketChannel`是监听连接进来的TCP连接；`DatagramChannel`用于接收和发送UDP数据包；`AsynchronousFileChannel`可以使文件读写异步
 ## Java NIO vs IO
|IO|NIO|
|--|---|
|Stream oriented|Buffer oriented|
|Blocking IO|Non blocking IO|
|-|Selectors|
