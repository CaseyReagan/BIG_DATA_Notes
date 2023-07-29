#  HIVE中如何查看表的物理存储大小

## 总结
+ （1）用 'desc formatted table_name;'命令查看表的hdfs location
+ （2）用相关hadoop命令查看表存储大小

### （一）背景
有一些报表数据的需求，每天跑出来的数据结果很多，需要根据每日数据表的大小来决定，是通过SFTP方式传输给下游，还是放在共用交换数据的数据库交给下游。

### （二）查看表信息
+ 方法1：查看表的基础字段信息
```sql
desc table_name;
```
+ 方法2：查看表的字段信息及表的一些配置信息
```sql
desc extended table_name;
```
+ 方法3：查看表的字段信息及元数据存储路径
```sql
desc formatted table_name;
```
在这种情况下，可以用方法3来查看存储路径。举例。
![图片](查看表详细信息.jpg)

### （三）查看表的存储大小
在获取表的hdfs地址之后使用如下命令，查看表的存储大小
```sql
$ hadoop fs -du /user/hive/warehouse/table_name|awk '{ SUM += $1 } END { print SUM/(1024*1024*1024)}'
or
$ hadoop fs -du /user/hive/warehouse/table_name/partition_column|awk '{ SUM += $1 } END { print SUM/(1024*1024*1024)}'
```