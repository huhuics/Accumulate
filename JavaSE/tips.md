 + Java反射中，`method.setAccessible(true)`并不是为`true`就能访问为`false`就不能访问，值为`true`则指示反射的对象在使用时应该取消 Java 语言访问检查（在大量反射调用过程中，性能可提升20倍左右）；值为`false`则指示反射的对象应该实施 Java 语言访问检查
 + AOP中，`Aspect`(切面)类是编写需要横切逻辑的代码，例如性能监控、日志等；通过一个条件来匹配想要拦截的类，这个条件称为`Pointcut`(切点)
 + 如果使用`CGLib`对目标类写了一个增强类，该增强类不能再次被CGLib自己进行增强，即CGLib不支持嵌套增强
 + 一个SOAP(`Simple Object Access Protocol`)请求实际上是通过HTTP协议的POST方式来传输数据的，只不过请求的Header中加入一些标识来说明自己是一个SOAP请求。这些标识说明了要调用的远程地址、方法名、参数名、参数值各是什么
 + WSDL(`Web Service Description Language`)是使用XML来描述WebService有哪些方法、参数类型、访问路径等
 + SOA(`Service-Oriented Architecture`)并不是Web Service，SOA是一种软件设计思想和架构模式；Web Service只是实现SOA的一种方式
