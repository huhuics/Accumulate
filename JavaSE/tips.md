+ Java反射中，`method.setAccessible(true)`并不是为`true`就能访问为`false`就不能访问，值为`true`则指示反射的对象在使用时应该取消 Java 语言访问检查（在大量反射调用过程中，性能可提升20倍左右）；值为`false`则指示反射的对象应该实施 Java 语言访问检查

+ AOP中，`Aspect`(切面)类是编写需要横切逻辑的代码，例如性能监控、日志等；通过一个条件来匹配想要拦截的类，这个条件称为`Pointcut`(切点)。AOP将那些影响多个类的行为封装到可重用的模块中

+ SOAP(`Simple Object Access Protocol`)是一种简单基于xml的轻量协议。一个SOAP请求实际上是通过HTTP协议的POST方式来传输数据的，只不过请求的Header中加入一些标识来说明自己是一个SOAP请求。这些标识说明了要调用的远程地址、方法名、参数名、参数值各是什么。可认为SOAP请求是HTTP POST的一个专用版本

+ WSDL(`Web Service Description Language`)是使用XML来描述WebService有哪些方法、参数类型、访问路径等

> Web Service(简称WS)使用SOAP协议作为RPC的序列化标准。SOAP是XML描述的，或者说其本身就是XML的子集，SOAP的传输需要**介质**，这个介质可以是HTTP、TCP甚至JMS。WS使用WSDL用于描述WS接口，而WSDL同样也是用XML语言描述的。

+ SOA(`Service-Oriented Architecture`)并不是Web Service，SOA是一种软件设计思想和架构模式；Web Service只是实现SOA的一种方式

+ REST(`Representational State Transfer`)是一种轻量级的Web Service架构，可以完全通过HTTP协议实现，其实现和操作比SOAP和XML-RPC更为简洁，性能、效率和易用性都优于SOAP协议

+ 通过Java反射机制创建类的实例对象主要有两种方法：    
    1. Class.newInstance()    
    2. 调用类对象的构造方法。
    
通过`Class.newInstance()`创建类的实例，本质是执行了类对象的默认空参的构造方法，如果一个类没有空参的构造方法，则会抛出`java.lang.NoSuchMethodException`异常
    
+ `java.lang.Class.forName(String name, boolean initialize, ClassLoader classLoader)`如果*initialize*为**false**，则表示JVM不会执行该类的static区域代码段，为**true**表示执行static区域代码段

+ 要想创建出能够处理复杂任务的程序，需要做到关注点分离——使设计中的每个部分都得到单独的关注。在分离的同时，也需要维持系统内部复杂的交互关系。这也叫低耦合、高内聚

+ 各层之间是松散连接的，层与层的依赖关系只是单向的。上层可以直接使用或操作下层元素，方式通过调用下层元素的公共接口，保持对下层元素的引用，或者采用常规的交互手段。而如果下层元素需要与上层元素进行通信（不只是返回方法调用值），则需要采用另一种通信机制，使用架构模式来连接上下层，如回调模式或观察者模式。
