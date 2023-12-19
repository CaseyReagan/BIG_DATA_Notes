#  MySQL中LIMIT关键字详解
本文借鉴：1.https://blog.csdn.net/weixin_53823776/article/details/130678799

### （一）背景
在HIVE里用limit和在oracle里用row num的时候，对Limit的具体实现产生了一些疑问。
***

### （二）Mysql中的LIMIT关键字 LIMIT m,n
Limit的下标默认从0开始，第一个参数是下标，查数据的话是从第一条开始查，第二参数就限定了查几条数据。
```sql
--检索记录为1-4行数据
Select * from table limit 0,3; 

--检索记录为6-最后一行数据
Select * from table limit 5,-1; 
```
***


### （三）Mysql中的LIMIT关键字 LIMIT n
Limit参数的使用，分为两种情况：【limit n】和【limit m,n】。
当Limit只有一个参数时，即参数值为多少就查询表中前多少条数据，返回n条数据，Limit n 等价于Limit  0，n。   

```sql
-- 检索前4个记录
Select * from table limit 4; 
```
***


### （四）利用 LIMIT 1 优化SQL效率
在某些情况下，如果已知查询结果只有一个，SQL结果中使用Limit 1会提高查询效率。比如表有唯一主键的列，查询某个主键值就可以LIMIT 1,Limit 1 只要找到了对应的一条记录，就不会继续往下扫描，效率会大大的提高（如果没有加上Limit 1 数据库会在找到符合条件的数据后，继续往下查询另一个符合条件的记录，直到最后一条数据）。
+ 【例1】查询成绩最高的学生姓名和成绩
```sql
Select Students name,score
from students
order by score desc
limit 1;
```

***
