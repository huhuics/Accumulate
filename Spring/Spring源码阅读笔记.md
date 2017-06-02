1. Spring4系统架构图
 ![Spring4系统架构图](https://github.com/huhuics/Accumulate/blob/master/image/SpringFrameworkRuntime.png?raw=true)
2. 核心容器由spring-beans、spring-core、spring-context和spring-expression（Spring Expression Language, SpEL） 4个模块组成
   + spring-beans和spring-core模块是Spring框架的核心模块，包含了控制反转（Inversion of Control, IoC）和依赖注入（Dependency Injection, DI）。BeanFactory 接口是Spring框架中的核心接口，它是工厂模式的具体实现。BeanFactory 使用控制反转对应用程序的配置和依赖性规范与实际的应用程序代码进行了分离。但 BeanFactory 容器实例化后并不会自动实例化 Bean，只有当 Bean 被使用时 BeanFactory 容器才会对该 Bean 进行实例化与依赖关系的装配。
   + spring-contest模块构架于核心模块之上，他扩展了BeanFactory，为她添加了Bean生命周期控制、框架事件体系以及资源加载透明化等功能。此外该模块还提供了许多企业级支持，如邮件访问、远程访问、任务调度等，ApplicationContext是该模块的核心接口，她是 BeanFactory 的超类，与 BeanFactory 不同，ApplicationContext 容器实例化后会自动对所有的单实例 Bean 进行实例化与依赖关系的装配，使之处于待用状态。
   + spring-expression模块是统一表达式语言（unified EL）的扩展模块，可以查询、管理运行中的对象，同时也方便的可以调用对象方法、操作数组、集合等。
3. Aop和设备支持 由spring-aop、spring-aspects和spring-instrumentation 3个模块组成
   + spring-aop是Spring的另一个核心模块，是Aop主要的实现模块。在Spring中，他是以JVM的动态代理技术为基础。
   + spring-aspects模块集成自AspectJ框架，主要是为Spring Aop提供多种Aop实现方法。
   + spring-instrumentation模块是基于JAVA SE中的"java.lang.instrument"进行设计的，应该算是Aop的一个支援模块，主要作用是在JVM启用时，生成一个代理类，程序员通过代理类在运行时修改类的字节，从而改变一个类的功能，实现Aop的功能。
4. 数据访问及集成 由spring-jdbc、spring-tx、spring-orm、spring-jms和spring-oxm 5个模块组成
   + spring-jdbc模块是Spring 提供的JDBC抽象框架的主要实现模块，用于简化Spring JDBC。主要是提供JDBC模板方式、关系数据库对象化方式、SimpleJdbc方式、事务管理来简化JDBC编程，主要实现类是JdbcTemplate、SimpleJdbcTemplate以及NamedParameterJdbcTemplate。
   + spring-tx模块是Spring JDBC事务控制实现模块。事务控制是绝对应该放在业务层的；但是，持久层的设计则应该遵循一个很重要的原则：保证操作的原子性，即持久层里的每个方法都应该是不可以分割的。
   + spring-orm模块是ORM框架支持模块，主要集成 Hibernate, Java Persistence API (JPA) 和 Java Data Objects (JDO) 用于资源管理、数据访问对象(DAO)的实现和事务策略。
   + spring-jms模块（Java Messaging Service）能够发送和接受信息。
   + spring-oxm模块主要提供一个抽象层以支撑OXM（OXM是Object-to-XML-Mapping的缩写，它是一个O/M-mapper，将java对象映射成XML数据，或者将XML数据映射成java对象）
5. Web 由spring-web、spring-webmvc、spring-websocket和spring-webmvc-portlet 4个模块组成
   + spring-web模块为Spring提供了最基础Web支持，主要建立于核心容器之上，通过Servlet或者Listeners来初始化IoC容器，也包含一些与Web相关的支持
   + spring-webmvc模块众所周知是一个的Web-Servlet模块，实现了Spring MVC（model-view-controller）的Web应用。
   + spring-websocket模块主要是与Web前端的全双工通讯的协议。
   + spring-webmvc-portlet模块是知名的Web-Portlets模块
6. 报文发送 即spring-messaging模块。
7. Test 即spring-test模块。
8. 各模块依赖关系
![](https://github.com/huhuics/Accumulate/blob/master/image/Spring%E5%90%84%E6%A8%A1%E5%9D%97%E4%BE%9D%E8%B5%96%E5%85%B3%E7%B3%BB.jpg?raw=true)
   + 从图中可以看出，IoC 的实现包 spring-beans 和 AOP 的实现包 spring-aop 是整个框架的基础，而 spring-core 则是整个框架的核心，基础的功能都在这三个包里。
   + 在此基础之上，spring-context 提供上下文环境，为各个模块提供粘合作用。
   + 在 spring-context 基础之上提供了 spring-tx 和 spring-orm包，而web部分的功能，都是要依赖spring-web来实现的。
   + 如果你想加入spring源码的学习，笔者的建议是从spring-core入手，其次是spring-beans和spring-aop，随后是spring-context，再其次是spring-tx和spring-orm，最后是spring-web和其他部分。
9. IoC也被称为依赖注入(DI)。它是一个处理对象依赖项的过程，也就是将他们一起工作的其他对象，通过构造参数、工厂方法实例化后再设置属性。当创建bean后，IoC容器再将这些依赖项注入进去。
10. Spring通过XmlBeanFactory类解析XML配置文件，将配置文件中配置的bean封装成BeanDefinition后载入到IoC容器，就可以在容器中建立数据映射。再进行BeanDefinition注册，这样最初的容器就可用了。
11. AbstractBeanFactory的doGetBean方法是依赖注入的实际入口，而依赖注入触发的前提是BeanDefinition数据已经建立好。
12. Spring IoC容器的核心是BeanFactory和BeanDefinition，分别对应对象工厂和依赖配置的概念。虽然我们通常使用的是ApplicationContext的实现类，但ApplicationContext只是封装和扩展了BeanFactory的功能，无论是通过XML配置还是注解实现依赖注入，最后都会将bean的定义映射成BeanDefinition。
13. Bean在XML文件里的展现形式是```<bean id="...">...</bean>```，当这个节点被加载到内存，就被抽象为BeanDefiniton了，在XML Bean节点中的那些关键字，在BeanDefinition中都有相对应的成员变量。Spring通过定义BeanDefinition来管理基于Spring的应用中各种对象以及它们之间的相互依赖关系。BeanDefinition抽象了我们对Bean的定义，是让容器起作用的主要数据类型。
14. AOP可以帮助我们为分散的对象引入公共的行为，它的作用主要是用来描述分散在对象、类或函数中的横切关注点，从关注点中分离出横切关注点是AOP的核心概念。
15. 在编译器修改源代码、运行期字节码加载前修改字节码或字节码加载后动态创建代理类的字节码，这是AOP的具体**实现方法**，而它是由三个重要步骤来完成的，首先是**生成代理对象**，然后是**拦截器的作用**，最后是**编织的具体实现**。
16. Spring AOP内部使用JDK动态代理或者CGLIB来为目标对象创建代理。如果被代理的目标对象实现了至少一个接口，则会使用JDK动态代理，所有该目标类型实现的接口都将被代理。若该目标对象没有实现任何接口，则创建一个CGLIB代理。尽管CGLIB效率更高，但还是推荐使用JDK动态代理。
17. 网络参考模型  
![](https://github.com/huhuics/Accumulate/blob/master/image/%E7%BD%91%E7%BB%9C%E5%8F%82%E8%80%83%E6%A8%A1%E5%9E%8B.jpg)
18. 网络模型层次图  
![](https://github.com/huhuics/Accumulate/blob/master/image/%E6%A8%A1%E5%9E%8B%E5%B1%82%E6%AC%A1%E5%9B%BE.jpg)  
19. Spring MVC框架是一个基于请求驱动的web框架，使用前端控制器模式来进行设计，再根据请求映射规则发送给相应的页面控制器（动作/处理器）进行处理。
20. Spring MVC请求流程图    
![](https://github.com/huhuics/Accumulate/blob/master/image/SpringMVC.jpg)    
    + 请求先到达一个前端控制器Servlet——DispatcherServlet，它的任务是根据请求信息（如URL）将请求发送给Spring MVC控制器
    + 页面控制器接收到请求后，进行功能处理，处理完毕后返回一个ModelAndView（模型数据和逻辑视图名）
    + 前端控制器收回控制权，根据返回的逻辑视图名，选择相应的视图进行渲染，并把模型数据传入以便视图渲染
    + 前端控制器再次收回控制权，将响应返回给用户
