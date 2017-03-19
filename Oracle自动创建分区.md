## Oracle自动创建分区
- 建表
```
create table day_interval_partition_table
(id number,
time_col date)
partition by range(time_col)
interval(numtodsinterval(1,'day'))
(
     partition p_day_1 values less than (to_date('2016-01-01','yyyy-mm-dd'))
);
```
- 查看表当前分区
```
select table_name,partition_name from user_tab_partitions where table_name='DAY_INTERVAL_PARTITION_TABLE';
```
- 插入数据
```
begin
  for i in 1 .. 12 loop
    insert into DAY_INTERVAL_PARTITION_TABLE
    values
      (i, trunc(to_date('2016-01-01', 'yyyy-mm-dd') + i));
  end loop;
  commit;
end;
```
**注意**  
如果是按月自动建分区，则numtodsinterval(1,'day')需要改成numtoyminterval(1,'month')
