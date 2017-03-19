## Spring事务隔离级别
> 事务的隔离级别定义了一个事务可能受其它并发事务活动影响的程度。可以把事务的隔离级别想象成事务间的“自私”程度  

在典型的应用中，多个事务同时运行，并发是必须的，所以经常会操作同一个数据，这容易导致如下问题：  
+ **脏读(dirty read)**
  - 脏读发生在一个事务读取了被另一个事务改写但尚未提交的数据时。如果这些改变在稍后回滚了，那么第一个事务读取的数据就是无效的  
  ![](http://www.byteslounge.com/imgs/spring-transaction-isolation-tutorial/dirty_read.png)
+ **不可重复读(non-repeatable read)**
  - 不可重复读发生在一个事务执行相同的两次或两次以上查询，但每次查询的结果都不相同。这通常是由于另一个并发事务在两次查询之间更新了数据  
  ![](http://www.byteslounge.com/imgs/spring-transaction-isolation-tutorial/non_repeatable_read.png)
+ **幻读(phantom read)**
  - 幻读和不可重复读相似。当一个事务(T1)读取几行记录后，另一个并发事务(T2)插入了一些记录时，幻影读就发生了。在后来的查询中，T1就会发现一些原来没有的额外记录  
  ![](http://www.byteslounge.com/imgs/spring-transaction-isolation-tutorial/phantom_read.png)
  
  在理想情况下，事务之间将完全隔离，从而可以防止这些问题的发生。然而完全隔离会影响性能，因为隔离经常涉及到涉及数据库中的记录，这会阻碍并发。  
  
|隔离级别|含义|
|-------|----|
|ISOLATION.DEFAULT|使用后端数据库默认的事务隔离级别|
|ISOLATION.READ_UNCOMMITTED|允许读取尚未提交的更改。可能导致脏读、不可重复读和幻读|
|ISOLATION.READ_COMMITTED|不允许读取尚未提交的更改。只能从已提交的事务中读取数据。可防止脏读，但仍会导致不可重复读和幻读|
|ISOLATION.REPEATABLE_READ|对相同字段的多次读取的结果是一致的，除非数据被当前事务改变。可防止脏读和不可重复读，但幻读仍有可能|
|ISOLATION.SERIALIZABLE|完全服从ACID隔离级别，确保不发生脏读、不可重复读和幻读。所有隔离级别中最慢的，因为它通常是通过完全锁定当前事务所涉及的数据表来完成的|  

  - Oracle数据库支持三种隔离级别：READ_COMMITTED, SERIALIZABLE以及oracle自带的READ_ONLY
  - MySQL数据库四种都支持  
  
  
||dirty read|non-repeatable read|phantom read|
|----|---------|-------------------|------------|
|READ_UNCOMMITTED|yes|yes|yes|
|READ_COMMITTED|no|yes|yes|
|REPEATABLE_READ|no|no|yes|
|SERIALIZABLE|no|no|no|


**[文中图片来源](http://www.byteslounge.com/tutorials/spring-transaction-isolation-tutorial)**
