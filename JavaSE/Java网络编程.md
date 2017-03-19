# Java网络编程
 + `socket`网络上的两个程序通过一个双向的通信连接实现数据交换，这个连接的一端称为一个`socket`。`socket`本质是编程接口(API)，对TCP/IP的封装。`HTTP`是轿车，提供了封装或者显示数据的具体形式；`socket`是发动机，提供了网络通信能力
 + Java网络API(standard API, not NIO)实现网络通信都是通过`InputStream`和`OutputStream`实现的
 + 使用`UDP`在客户端和服务器端是不存在连接的
 + `TCP`和`UDP`监听端口不是一样的，这意味着`TCP`和`UDP`都监听同一个端口，例如80，而不会产生冲突
 + A Java Servlet is a Java Object that responds to HTTP requests. It runs inside a Servlet container.Here is an illustration os that:  
  ![servlet](https://github.com/huhuics/Accumulate/blob/master/image/Servlets%20inside%20a%20Java%20Servlet%20Container.png)
 + The browser sends an HTTP request to the Java web server. The web server checks if the request is for a servlet. If it is, the servlet container is passed the request. The servlet container will then find out which servlet the request is for, and activate that servlet. The servlet is activated by calling the `Servlet.service()` method. Once the servlet has been activated via the `service()` method, the servlet processes the request, and generates a response. The response is then sent back to the browser.
 + The life cycle contains the following steps:  
   1. Load Servlet Class  
   2. Create instance of Servlet  
   3. Call the servlets `init()` method  
   4. Call the servlets `service()` method  
   5. Call the servlets `destory()` method  
   Typically, only a single instance of the servlet is created, and concurrent reqeusts to the servlet are executed on the same servlet instance.
   Step 1, 2 and 3 are executed only once, when the servlet is initially loaded. By default the servlet is not loaded until the first request is received for it. You can force the container to load the servlet when the container starts up by config web.xml though.  
   For every request received to the servlet, the servlets `service()` method is called.  
   Step 5 is executed when the servlet container unloads the servlet, which is only executed once.
