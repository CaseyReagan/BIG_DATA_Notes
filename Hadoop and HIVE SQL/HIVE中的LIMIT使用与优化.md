#  HIVE中的LIMIT使用与优化
本文借鉴：1.https://www.cnblogs.com/hark0623/p/5054701.html
2.https://blog.csdn.net/weixin_42201058/article/details/82179235

### （一）背景
使用HIVE的LIMIT时候对效率产生了疑问，想看HIVE中的LIMIT是否是抽样采样。
***

### （二）LIMIT的使用
详见【MySQL中LIMIT关键字详解】，语法略过。
***

### （三）在HIVE中使用LIMIT的（特有的）SQL优化
+ 举例
```sql
--在hive表前1000行里，过滤出不重复的refid,imsi。
--错误的写法：
select distinct refid,imsi from HIVE_D_MT_UU_H_SPARK limit 1000;
--会去读取全表，把0~1000行的不重复refid,imsi显示出来。

--正确的写法：
select distinct refid,imsi from (select * from HIVE_D_MT_UU_H_SPARK limit 1000);

--调优的写法：
CREATE TABLE TEMP_HIVE_D_MT_UU_H_SPARK AS
select * from HIVE_D_MT_UU_H_SPARK limit 1000;
select distinct refid,imsi from TEMP_HIVE_D_MT_UU_H_SPARK;
```
【重点】hive最快的执行就是不走MapReduce。简单的select的是最快的，嵌套啥的都比较忙。与关系型数据库不同。调优的写法执行更快。

***

### （四）从HIVE参数中调参优化
将set hive.limit.optimize.enable=true 时，limit限制数据时就不会全盘扫，而是根据限制的数量进行抽样。
```sql
--用set命令查看HIVE数据库所有参数配置
hive> set;
```
其他的参数配置：   
1、hive.limit.row.max.size    -- 设置最小的采样容量，我理解这个是控制最大的抽样数量   
2、 hive.limit.optimize.limit.file -- 设置最大的采样样本数我理解这个是抽样的最大文件数量
***
