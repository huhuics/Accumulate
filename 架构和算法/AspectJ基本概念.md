## Join Point    
连接点。Join Point是AspectJ中的一个重要概念，它可以被看作是程序运行时的一个执行点，比如一个方法调用可以看作是个Join Point，如Log.info()这个方法，info()可以看作是个Join Point。常见的Join Point有    
+ method call 方法调用    
+ method execution 方法执行    
+ constructor call 构造方法调用    
+ constructor execution 构造方法执行    
+ field get 获取某个变量    
+ field set 设置某个变量    
+ handler 异常处理，如try catch中的catch内    

实际上，也就是想把新的代码插在程序的哪个地方，是插在构造方法中，还是插在某个方法调用前，或者是插在某个方法中，这个地方就是Join Point。当然不是所有的地方都运行插入新的代码。    

## PointCut    
切点。一个程序会有多个Join Point，即使是同一个方法，也还分为call和execution类型的Join Point。但并不是所有的Join Point都是我们关心的。Point Cut就是提供一种使得开发者选择自己需要的Join Point的方法，可以理解为基于表达式的拦截条件。    

## Advice    
Advice就是我们以何种方式插入代码，有Before、After和Around。    

