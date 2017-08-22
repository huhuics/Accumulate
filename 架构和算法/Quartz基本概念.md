## Quartz    
### Quartz API    
+ **Scheduler**    
    - 代表一个调度容器，一个调度容器可以注册多个JobDetail和Trigger。一个Scheduler如果被关闭了，除非被重新实例化，否则不能进行调度。    
+ **Job**    
    - 接口，表示一个具体的执行任务。一个job可以被多个trigger关联。每次调度执行Job时，scheduler都会创建一个新的job实例；每次调用完，会销毁该实例对象。job类必须是public的，且必须有无参的构造方法    
+ **JobDetail**    
    - 表示一个具体可执行的调度程序，Job是这个可执行调度程序所要执行的内容    
+ **Trigger**    
    - 触发器，用于定义任务调度时间规则    
+ **JobBuilder**    
    - 用于定义/创建JobDetail实例    
+ **TriggerBuilder**    
    - 用于定义/创建Trigger实例
    
### 几个注解    
+ **@DisallowConcurrentExecution**    
    - 在Job上添加此注解表示告诉Quartz不并发调度这个job    
+ **@PersistJobDataAfterExecution**    
    - 在Job上添加此注解表示，当job成功执行以后，Quartz会更新JobDetail中JobDataMap的值，使得下次调度时同一个job使用更新后的值。如果使用`@PersistJobDataAfterExecution`注解，最好是同时使用`@DisallowConcurrentExecution`，避免因线程竞争导致数据更新问题。    
    
#### Trigger    
+ Trigger可以设置优先级，默认优先级是5。当不同的trigger在同一时间触发且资源不足时，才会比较优先级。

