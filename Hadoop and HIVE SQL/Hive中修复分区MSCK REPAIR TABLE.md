# Hive中修复分区MSCK REPAIR TABLE
本文借鉴内容：https://blog.csdn.net/wsdc0521/article/details/102861542

## 总结
+ 建外部表的时候可以把这个命令放到最后做保险措施，不过感觉用处不大。
+ 修复删除分区内容的时候执行此命令

### （一）背景
做预付用户诈骗电话拦截任务的时候，借鉴IDD的预付NIFI任务，发现在任务的最后一步流程有执行这一句，于是检查一下这句的用处。
***

### （二）原理
我们知道hive有个服务叫metastore，这个服务主要是存储一些元数据信息，比如数据库名，表名或者表的分区等等信息。如果不是通过hive的insert等插入语句，很多分区信息在metastore中是没有的。   
Hive将每个表的分区信息保存在metastore中，如果通过 fs -put命令直接将分区信息添加到HDFS，metastore是不会感知到这些新增的分区，除非执行了ALTER TABLE table_name ADD PARTITION命令。但是用户可以运行metastore检查命令：
```sql
MSCK REPAIR TABLE table_name;
```
该命令将关于分区的元信息添加到Hive metastore中，这是对于那些没有元信息的分区来说的。换句话说，就是将任何存在于HDFS上但不在metastore上的分区添加到metastore。
***

### （三）用处
常用于手动复制目录到hive表的location下，此时Hive元数据中没有记录到该目录是hive的分区，所以查不到该分区数据。例子：   
表test_tbl，存储路径为/user/hive/warehouse/test_tbl，分区字段为pt，正常添加分区是这样的：
```sql
alter table add if not exist partition (pt='20190101');
```
然后在hdfs中表的存储目录下会生成分区目录/user/hive/warehouse/test_tbl/pt=20190101，此时无论是往该目录新增数据文件还是往hive中insert数据，之后都可以在hive中查到该分区的数据。   
但如果直接将一个文件夹名为pt=20190201的上传至hdfs的/user/hive/warehouse/test_tbl/路径下，在hive中是查不到分区pt='20190201'的数据的，就如前面所说，hive的metadata中没有记录到分区20190201这个分区，因此需要执行msck repair table命令修复。   
   
而在我们的NIFI流程任务中，是先建立了分区，再fs -put了数据进去，如果分区建立不成功，那么后续put也会Put成一个文件，此时修复是没用的，需要先将放进去的该文件先删除，然后再重新建立分区和put数据文件。但是如果我们直接put一个以分区命名的文件夹过去，这时使用MSCK REPAIR TABLE就可以成功建立分区。