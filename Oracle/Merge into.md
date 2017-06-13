## 1.Merge into 基本用法介绍
+ 使用场景
    - 当遇到大量需要insert/update的时候，也就是存在记录就更新(update)，不存在就插入(insert)
+ 基本语法
```SQL
MERGE INTO table_name alias1   
USING (table|view|sub_query) alias2  
ON (join condition)   
WHEN MATCHED THEN   
    UPDATE table_name   
    SET col1 = col_val1,   
        col2     = col2_val   
WHEN NOT MATCHED THEN   
    INSERT (column_list) VALUES (column_values);  
```
上面的语法意思是：在alias2中select出来的数据，每一条都跟alias1进行ON (join condition) 比较，如果匹配，就进行更新(update)的操作，如果不匹配，就进行插入(insert)操作。    
因此，在一个同时存在update和insert的merge语句中，总共update/insert的记录数，就是USING语句中alias2的记录数。
## 2.举例
有一张表T，有两个字段a和b，如果存在则更新T表中的b值，不存在则插入一条记录。根据merge into的语法写成的SQL语句如下：
```SQL
#不能insert
MERGE INTO T T1  
USING (SELECT a,b FROM T WHERE t.a='1001') T2  
ON ( T1.a=T2.a)  
WHEN MATCHED THEN  
  UPDATE SET T1.b = 2  
WHEN NOT MATCHED THEN   
  INSERT (a,b) VALUES('1001',2); 
```
我们会发现，如果没有1001的记录时，不能做insert操作。这是因为如果`USING`中的SQL查不到记录，就不会执行后面的`MATCHED`或`NOT MATCHED`。    
将SQL做如下修改即可：
```SQL
MERGE INTO T T1  
USING (SELECT '1001' AS a,2 AS b FROM dual) T2  
ON ( T1.a=T2.a)  
WHEN MATCHED THEN  
  UPDATE SET T1.b = T2.b  
WHEN NOT MATCHED THEN   
  INSERT (a,b) VALUES(T2.a,T2.b); 
```
这样保证了`USING`中每次都能查到记录。    
 **注意：** 如果不注意，merge into将会是比较危险的SQL，比如想更新一条记录的时候，因为不注意，可能将整表都update。如下语句，如果`ON`中的条件恒成立，则update整张表记录：
```SQL
#整表update
MERGE INTO T T1  
USING (SELECT Count(*) cnt FROM T WHERE T.a='1001') T2  
ON (T2.cnt>0)  
WHEN MATCHED THEN  
  UPDATE SET T1.b = T2.b  
WHEN NOT MATCHED THEN   
  INSERT (a,b) VALUES(T2.a,T2.b);  
```
## 3.其它
 + insert和update是可选的
 + update和insert后面可以跟where子句
 + 在on条件中可以使用常量来insert所有行到目标表，不需要连接到源表和目标表
 + update子句后面可以跟delete来去除一些不需要的行    
```SQL
#update子句后跟delete
merge into products p  
using newproducts np  
on (p.product_id = np.product_id)  
when matched then  
  update  
     set p.product_name = np.product_name delete  
   where category = 'ELECTRNCS';  
```
