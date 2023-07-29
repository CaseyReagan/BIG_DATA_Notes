# HIVE中count(*)为0的坑

### （一）背景
遇到个问题，在hive里对表使用count(*)统计行数的时候，返回值为0.   
注意，这个问题在带条件查询的时候不会有问题，只存在于对全表统计。
***
### （二）解决方案
（1） 在语句后加上limit 1可以解决。
```sql
select count（*） from table limit 1;
```
（2）改变HIVE的环境配置
```sql
set hive.compute.query.using.stats=fasle;
```
***
### （三）发生原因
究其根本，这是hive中的一个优化参数导致的，对于一些使用频率可能很高的sql会进行查询优化，会将这个参数[hive.compute.query.using.stats]设置为true(默认是false)，这样的话，Hive在执行某些查询时，例如select count(1)，只利用元数据存储中保存的状态信息返回结果，从而提高了响应速度
```sql
hive.compute.query.using.stats=true

Instructs Hive to use statistics when generating query plans
```
***
