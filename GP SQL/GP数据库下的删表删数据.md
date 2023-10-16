#  GP数据库下的删表删数据
本文借鉴：1. https://blog.csdn.net/sosemseo/article/details/109220247
2. https://blog.csdn.net/sunny05296/article/details/130924877

### （一）背景
GP数据库下如何正确的删表删数据，有好几个函数，该选用哪个函数，这值得研究。

***
### （二）Delete()函数删除数据
删数据用delete()函数，DELETE命令用于删除表中的一行或多行数据。它可以与WHERE子句一起使用，以便只删除满足特定条件的行。
```sql
DELETE FROM table_name WHERE condition;

```
+ （1）【注意】delete函数删了某一行或多行数据，是不动表结构和页结构的，检查表的物理大小是不会变的。要配合使用vacuum函数释放存储资源。例如：
```sql
-- 新建表open.t_ttt(20行数据)
select count(1) from open.t_ttt;
-- 20
select pg_relation_size('open.t_ttt');
-- 得到大小是1120Bytes
delete from open.t_ttt where f_id=2;
-- DELETE 10
select pg_relation_size('open.t_ttt');
-- 大小还是1120Bytes
vacuum open.t_ttt;
-- VACUUM
select pg_relation_size('open.t_ttt');
-- 得到大小是560Bytes
```
+ （2）【注意】delete把表数据删空后，这张表的结构和存储页也是存在的，不会自动删除消失掉。delete操作是可以撤销掉的。（没vacuum或释放存储资源前，这些数据依然存在只是被设为可写）

***
### （三）Truncate()函数删除整张表内数据
Truncate命令用于删除表中的所有数据，语法如下：
```sql
TRUNCATE TABLE table_name;

```
+ （1）【注意】该语句执行之后，整张表的内容都将被删除。但是不删除表本身。truncate操作是不可以回滚的，执行后数据无法恢复。且执行后会回收所有存储空间。比Delete命令快。

***
### （四）Drop()函数删掉整个表
Drop用于删除整张表，格式如下：
```sql
DROP TABLE table_name;

```
+ （1）【注意】该语句执行之后，整张表将被删除。不可恢复。

***
### （五）三者的区别
Delete删数据，不动表结构，可以恢复，不回收物理空间。   
truncate删某一个表内所有数据，不动表结构，数据不可恢复，回收空间。
Drop删整个表，删表结构，不可恢复，回收空间。

***
### （六）vacuum()函数

vacuum 是PG数据库垃圾回收，vacuum的主要动作：磁盘清理、更新统计信息、重组数据、解决事务ID回滚问题：   
1）清除 update、delete 操作后留下的死元组；
2）跟踪表块中可用空间，更新 free space map；
3）更新 VM 即 visibility map（可见性映射文件map，vacuum 会根据该文件来选择是否扫描某个 page。select * from pg_visibility('test01');）、index only scan，后续 vacuum 会用到，更新以后，提高后续 vacuum 的效率；
4）冻结表中的行，防止事务ID回滚；
5）配合 analyze、定期更新统计信息（pg_stat_all_tables）；   
vacuum 可以有效解决表膨胀的问题。vacuum有两种：标准vacuum 和 vacuum full：
1） 标准vacuum:  基本可以 online 操作（DML 运行正常，但不能执行 ALTER TABLE），执行速度快，但能回收的磁盘空间很少。
2） vacuum full: 能回收更多的磁盘空间，但运行速度要慢很多，它需要对表加独占锁（阻塞一切读写操作），因此不能与该表的其他操作并发进行；此外还需要额外空间存储表副本。

【注意】该动作会消耗系统一定的资源，引起系统的IO上升，对有一定系统瓶颈来说容易造成堵塞，严重会把GP宕掉，造成数据库瞬断。一般不建议vacuum库中全表，通常做法是vaccum指定的表。   
***