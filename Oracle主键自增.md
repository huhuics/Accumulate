## 设置主键自增
1. **创建序列**
```SQL
CREATE SEQUENCE SEQ_NAME  --序列名
INCREMENT BY 1    
START WITH 1  --从1开始  
NOMAXVALUE    --不设最大值  
NOCYCLE       --不循环  
NOCACHE;  
```
2. **创建触发器**
```SQL
create or replace trigger TRI_NAME  --触发器名
before insert on TABLE_NAME  --表名
for each row
begin
select SEQ_NAME.nextval into :new.ID from dual;  --sequence名
end;
```

## 查询时间运算
1. **numtodsinterval(<x>,<c>)**, x是一个数字，c是一个字符串表示x的单位，这个函数把x转为interval day to second数据类型，常用的单位有day, hour, minute, second
2. **numtoyminterval(<x>,<c>)**, 该函数与numtodsinterval类似，把x转为year或者month
```SQL
select sysdate, sysdate + numtodsinterval(3,'DAY') as res from dual; 
select sysdate, sysdate + numtoyminterval(1, 'MONTH') from dual;
select to_date('2016-01-01 16:22:36', 'yyyy-mm-dd hh24:mi:ss') + 1 + NumToYMInterval(1, 'MONTH')  from dual;
--结果：2016/2/2 16:22:36
```
