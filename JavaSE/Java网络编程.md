# Java网络编程
 + `socket`网络上的两个程序通过一个双向的通信连接实现数据交换，这个连接的一端称为一个`socket`。`socket`本质是编程接口(API)，对TCP/IP的封装。`HTTP`是轿车，提供了封装或者显示数据的具体形式；`socket`是发动机，提供了网络通信能力
 + Java网络API(standard API, not NIO)实现网络通信都是通过`InputStream`和`OutputStream`实现的
 + 使用`UDP`在客户端和服务器端是不存在连接的
 + `TCP`和`UDP`监听端口不是一样的，这意味着`TCP`和`UDP`都监听同一个端口，例如80，而不会产生冲突
