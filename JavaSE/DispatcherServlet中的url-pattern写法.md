### 一、Servlet容器对URL的匹配过程
当一个请求发送到servlet容器的时候，容器先将请求过来的URL去除应用上下文的路径作为servlet的映射url。比如在浏览器中输入 *http://localhost/test/abc.html* ，应用上下文是test，容器会将 *http://localhost/test* 去掉，剩下的*/abc.html*部分作为servlet的映射匹配。这会按照servlet-mapping在web.xml中声明的先后顺序进行匹配，且当一个servlet匹配成功以后，就不会去匹配其它的servlet（filter不同，filter会将符合条件的请求都进行过滤）。


### 二、什么是default servlet？
- web.xml中如果某个Servlet的映射路径**仅仅为一个斜杠(/)**，那么这个Servlet就成为当前web应用程序的缺省Servlet

- 凡是在web.xml文件中**找不到匹配的元素的URL**，它们的访问请求都将交给缺省Servlet处理。也就是说，**缺省Servlet用于处理所有其它Servlet都不处理的访问请求**。

- 在tomcat的安装目录/conf/web.xml文件中，注册了`org.apache.catalina.servlets.DefaultServlet`，并将这个Servlet设置为缺省的Servlet。

- 当访问tomcat服务器中某个静态html文件或图片时，实际上是在访问这个缺省的Servlet，由DefaultServlet类寻找相应资源。

- 如果在web应用程序的web.xml中配置了Servlet的映射路径为"/"，则这个Servlet将作为缺省的Servlet，当请求的URL和其它Servlet均匹配不到时，将交给这个缺省的Servlet处理。静态资源也是如此。


### 三、url-pattern的配置方式
**第一种：**

- '*.xxx'，以指定后缀结尾的请求都交由DispatcherServlet处理

**第二种：**

- '/'将会*覆盖容器的default servlet*，凡是在web.xml中找不到匹配的URL、静态资源都将交给该Servlet处理

**第三种：**

- '/*' **错误的配置**，使用这种配置如果最终要转发到一个JSP页面，将仍然会由`DispatcherServlet`解析，由于不能根据根据JSP页面找到handler，程序报错


### 四、为什么'/*'是错误的？
Tomcat的web.xml文件中配置了一个处理jsp的Servlet—— **org.apache.jasper.servlet.JspServlet**，它将拦截*.jsp和*.jspx。如果url-pattern设置为/*，则对jsp的请求将被DispatcherServlet拦截。


### 五、为什么'/'不会拦截\*.jsp和\*.jspx请求？
\*.jsp和\*.jspx请求将优先被JSPServlet拦截，因此不会被默认Servlet拦截。
