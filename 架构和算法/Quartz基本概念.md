## Quartz    
### API    
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

> Many Triggers can point to the same Job, but a single Trigger can only point to one Job.
    
### 几个注解    
+ **@DisallowConcurrentExecution**    
    - 在Job上添加此注解表示告诉Quartz不并发调度这个job    
+ **@PersistJobDataAfterExecution**    
    - 在Job上添加此注解表示，当job成功执行以后，Quartz会更新`JobDetail中JobDataMap`的值，使得下次调度时同一个job使用更新后的值。如果使用`@PersistJobDataAfterExecution`注解，最好是同时使用`@DisallowConcurrentExecution`，避免因线程竞争导致数据更新问题。    
    
#### Trigger    
+ Trigger可以设置优先级，默认优先级是5。当不同的trigger在同一时间触发且资源不足时，才会比较优先级    
+ `SimpleTrigger`几个属性：开始时间、结束时间、重复次数、重复时间间隔。如果重复时间间隔为0，则会引起任务的并发调度    
+ `CronTrigger`可以设置比较复杂的调度时间策略    
    - `?`只能用于日和周，表示“没有指定值”，但不能同时用于这两个时间    
    - `L`只能用于日和周，用于日表示每个月最后一天；用于周表示`SAT`，即周六。但如果用于周的其它数值之后，如`6L`或`FRIL`，则表示这个月的最后一个FRI    
    - `W`用于日，表示weekday（周一至周五），如`15W`表示离15号最近的weekday    
    
#### 监听器    
+ **TriggerListener**    
监听Trigger相关动作，如触发器执行、执行失败、执行完成    
+ **JobListener**    
监听Job相关动作，如任务执行前、执行完成    

使用监听器也很简单，是现实上述接口后，再注册到`Scheduler`。为方便起见，可以继承`JobListenerSupport`和`TriggerListenerSupport`，再覆盖需要监听的事件方法。    

+ **SchedulerListener**    
与Scheduler相关的事件包括添加job/trigger、删除job/trigger、关闭scheduler等等。    
使用`SchedulerListener`也很简单，实现`SchedulerListener`接口或者继承`SchedulerListenerSupport`类，再注册到`Scheduler`。

#### Job存储    
> 不要在代码中直接使用`JobStore`实例，而应该通过配置文件告诉Quartz使用哪种存储策略    

+ **RAMJobStore**    
将Job和Trigger数据存储在RAM中，如果系统发生故障，数据将丢失。    
+ **JDBCJobStore**    
    - `JobStoreTX` 如果不需要将调度命令与应用的事务关联，则使用JobStoreTX，这也是最常见的选择    
    - `JobStoreCMT` 需要Quartz和应用的事务关联。应用的JTA事务在任务调度前开始，在任务调度完成后提交事务    
    
#### 配置    
一个Quartz引用可用之前，需进行如下配置：    
+ ThreadPool：如果调度时没有线程可用，Quartz将阻塞任务直到有可用的线程或超过超时时间。
+ JobStore    
+ DataSource(if necessary)    
+ The Scheduler itself    

#### 其它说明    
+ **集群模式**    
    - 当使用`jobStoreTX, JobStoreCMT or TerracottaJobStore`时可以使用集群模式    
    - `org.quartz.jobStore.isClustered`配置成`true`    
    - 集群中每个Quartz实例会使用相同的quartz.properties配置文件
    - 集群中每个节点**必须**使用不同的`instanceId`
    - 使用集群时，节点之间的时间**必须**同步    
    
+ **插件**    
    - Quartz允许可以自定义插件，见`org.quartz.plugins`    

+ **JobFactory**    
    - 用户可以自定义`JobFactory`    
    
+ **Quartz自带工具**
    - 如`发送邮件`等



