#  GP查看分区非分区表的物理大小
本文借鉴内容：1.https://blog.csdn.net/sptoor/article/details/11170799

## 总结
+ （1）非分区用pg_relation_size()
+ （2）分区了自己新建一个函数


### （一）背景
有时候需要检查一个GP表所占物理空间的大小，甚至有时需要检查删数据之后表空间是否有释放，所以要检查GP表的大小。

***
### （二）GP非分区表查看大小
采用pg_relation_size()函数，及更好看的版本pg_size_pretty（）函数大小。用法。
```sql
select pg_relation_size('open.t_ttt');

select pg_relation_size('"TEST".tw_test_d');
--注意schema和表名要一起框进单引号里，有时schema还带着双引号，也要一同放进去。
-- 得到大小 298287104 Bytes

select pg_size_pretty(pg_relation_size('"TEST".tw_test_d'));
-- pretty版本是套在size函数外面的，做了个转换变成以MB记录的结果，更利于观察。
-- 得到大小 284 MB
```
请注意：如果对象是个GP的分区表，用这个函数将返回0，原因在于:GP的分区表的主表只是一个表定义,其实际数据内容存储在继承父表的分区子表里面了。

***
### （三）遇到GP分区表了该怎么办
GP分区表是无法用二里的函数直接查看的，原因如上，这个时候怎么办呢，只能采取自建一个函数来统计。可以使用如下代码。   
请注意：执行这个新建函数，需要gpadmin权限。
```sql
-- Function: calc_partition_table(character varying, character varying)
 
-- DROP FUNCTION calc_partition_table(character varying, character varying);
 
CREATE OR REPLACE FUNCTION calc_partition_table(v_schemaname character varying, v_tablename character varying)
  RETURNS bigint AS
$BODY$
DECLARE
    v_calc BIGINT := 0;
    v_total BIGINT := 0;
    v_tbname VARCHAR(200);
    cur_tbname cursor for select schemaname||'.'||partitiontablename as tb from pg_partitions
   where schemaname=v_schemaname and tablename=v_tablename;
BEGIN
    OPEN cur_tbname;
    loop
        FETCH cur_tbname into v_tbname;
        if not found THEN
            exit;
        end if;
        EXECUTE 'select pg_relation_size('''||v_tbname||''')' into v_calc;
        v_total:=v_total+v_calc;        
    end loop;
    CLOSE cur_tbname;
    RETURN v_total;
end;
$BODY$
  LANGUAGE plpgsql VOLATILE;
ALTER FUNCTION calc_partition_table(character varying, character varying) OWNER TO gpadmin;
```
   
使用方法举例：
```sql
--在admin账号下执行完以上代码后

select calc_partition_table('ue_test','dw_test');
-- 得到结果 94001397760
```
