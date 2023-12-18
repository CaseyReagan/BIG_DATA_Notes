#  MSsql与SqlS与Oracle中的LIMIT功能
本文借鉴：1.https://www.cnblogs.com/vickey123/p/17651706.html


### （一）背景
在使用上游oracle的数据库的时候，涉及一个很大的表的limit的功能，想捞取几条数据看看，oracle中没有LIMIT函数，应该用什么。
***

### （二）LIMIT函数在MySQL中
【limit函数详解见另一篇MySQL中LIMIT关键字详解】
limit 关键字在Mysql用于限制返回的查询结果的行数，语法：
```sql
# 查询emp表中的前 5条数据
select * from emp limit 5;
# 查询emp表中 从第4条开始,查询6条
select * from emp limit 3,6;
```
如果筛选条件带where，那么LIMIT n返回的就是查询出来满足条件的前n条结果。
***

### （三）SQL Server中的TOP关键字
在SQL Server中，不支持 Limit 语句，但是它支持 TOP。语法：
```sql
## 查询结果中前6条记录
select top 6 id from tablename

## 查询结果中第 7 条到第 9 条记录
select top 3 id from tablename
where id not in (
  select top 6 id from tablename
)

select top (n-m+1) id from tablename
where id not in (
  select top m-1 id from tablename
)
```
***

### （四）Oracle中的rownum函数
oracle数据库不支持mysql中limit功能，但可以通过rownum来限制返回的结果集的行数，rownum并不是用户添加的字段，而是oracle系统自动添加的。语法：
```sql
## 使查询结果最多返回前10行：
select * from CLEAR where rownum<=10;

## 使查询结果返回中间的10到100行：
select * from CLEAR where rownum<101 minus select * from CLEAR where rownum>9;

## 或者：
select * from (select t.*,rownum rn from CLEAR_01 t) nn where nn.rn>9 and nn.rn<101;
```
+ 注意：select * from CLEAR rownum>99 and rownum<101是错误的，oracle会认为条件不成立