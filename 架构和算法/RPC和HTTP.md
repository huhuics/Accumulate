Http和RPC并不是一个并行概念，RPC = Remote Produce Call，是一种技术的概念名词，Http是计算机网网络中应用层的一个协议，RPC是远程过程调用，其协议通常包括传输协议和序列化协议。RPC可以通过http来实现，也可以通过socket自己实现一套协议来实现。

传输协议：如gRPC使用的http2协议，或者dubbo自定义报文的tcp协议

序列化协议：如基于文本编码的xml、json，也有基于二进制编码的hessian等

那么使用自定义tcp协议的RPC框架和直接使用http有什么区别？

1.tcp是传输层协议，使用http协议的tcp报文包含太多信息，一个post请求的协议内容大致如下：
```xml HTTP/1.0 200 OK 
Content-Type: text/plain
Content-Length: 137582
Expires: Thu, 05 Dec 1997 16:00:00 GMT
Last-Modified: Wed, 5 August 1996 15:55:28 GMT
Server: Apache 0.84

<html>
  <body>Hello World</body>
</html>
```
即使该请求的body内容使用二进制编码，但报文元数据也就是header头的键值对却使用文本编码，非常占用字节。

而如果使用自定义tcp协议进行传输报文，可以极大地精简传输内容。但缺点是缺乏通用性。

2.成熟的RPC框架相对于http容器，封装了服务发现、负载均衡、熔断降级等面向服务的高级特性，良好的RPC框架是面向服务的封装，针对服务的可用性和效率等都做了优化，单纯使用http调用则缺少这些特性。

3.发起调用时，使用http调用每次都要写一串发起http请求的代码，而RPC调用则像调用本地方法一样去发起远程调用，让使用者感知不到远程调用的过程。

4.http适用于在接口不多、系统与系统交互较少的情况下，解决信息孤岛初期常用的一种通信手段，优点是简单、直接、开发方便。且以Restful规范为代表，它可读性好，跨语言。

5.RPC适用于大型复杂系统，内部系统、接口非常多的情况。RPC使用的是长连接，不必每次通信都要像http一样三次握手四次挥手，减少网络开销。

6.RPC框架一般都有注册中心，监控完善，且对于接口的发布、下线、动态扩展等，对调用方来说是无感知的。

> RPC = socket + 动态代理

![Java高级开发工程师](image/RPC.png)
