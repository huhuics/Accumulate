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
    
    
