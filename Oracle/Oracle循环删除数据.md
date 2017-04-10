```SQL
declare
  maxrows   number default 10000; #事务默认提交数据量
  delete_ct number default 0;     #循环次数,通过计算得到
begin
  select count(1) / maxrows
    into delete_ct
    from [table_name]
   where gmt_create >= to_date(concat('2016-09-03', ' 00:00:00'), 'yyyy-mm-dd hh24:mi:ss')
     and gmt_create <= to_date(concat('2016-10-03', ' 23:59:59'), 'yyyy-mm-dd hh24:mi:ss');
   for i in 1 .. TRUNC(delete_ct) + 1 
     loop 
       delete [table_name]
       where gmt_create >= to_date(concat('2016-09-03', ' 00:00:00'), 'yyyy-mm-dd hh24:mi:ss')
         and gmt_create <= to_date(concat('2016-10-03', ' 23:59:59'), 'yyyy-mm-dd hh24:mi:ss')
         and rownum <= maxrows;
       commit;
     end loop;
end;
```
