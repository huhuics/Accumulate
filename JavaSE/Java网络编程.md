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
   For every request received to the servlet, the servlets `service()` method is called by web server.  
   Step 5 is executed when the servlet container unloads the servlet, which is only executed once.  
 + `HttpServletResponse.sendRedirect()`方法和`RequestDispatcher.forward()`方法都可以利用其它资源(Servlet, jsp, html)来为客户端服务，但这两个方法是有区别的。  
   1. 重定向sendRedirect()交互：浏览器访问servlet1，servlet1想让servlet2位客户端服务，servlet1调用sendRedirect方法，将客户端的请求重定向到servlet2，浏览器访问servlet2，servlet2对客户端请求做出相应。  
   调用sendRedirect方法，实际是告诉浏览器servlet2所在位置，让浏览器重新访问servlet2，因此浏览器地址栏的URL将会改变。此过程对用户是透明的。sendRedirect不但可以在位于同一主机上相同或者不同web应用程序进行重定向，而且可以将客户端重定向到其它服务器web应用程序资源。  
   2. forward交互：浏览器访问servlet1，servlet1想让servlet2对客户端的请求进行响应，于是调用forward方法，将请求转发给servlet2处理，servlet2对请求做出响应。  
   调用forward方法，浏览器并不知道为其服务的servlet已经换成了servlet2，它只知道发出一个请求获得一个响应，地址栏不变。
