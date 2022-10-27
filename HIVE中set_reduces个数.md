# HIVE中set reduces个数
本文借鉴内容：1.https://blog.csdn.net/zjjcchina/article/details/123845563


### （一）背景
用sort by的时候看到说可以设置reducer的个数，然后发现默认为-1.
***

### （二）默认设置
在默认情况下
```sql
set mapreduce.job.reduces=-1

--通过这句设置
set mapreduce.job.reduces= 3; --设置为3个
```
实际运行计算过程中reducer的数量会由所读取文件的大小来决定。文件默认大小是256M，即每256M对应一个reduce。比如当文件大小为1G时，会启用4个reducer处理数据；当文件大小为400M时，会启用2个reducer来处理。
***

### （三）其他设置场景
在进行分区或者sort by 操作时，需要设置mapreduce.job.reduces的数量，此时实际启用的reducer的个数等于设置值。   
在进行分桶操作的情况下，当 set mapreduce.job.reduces=-1或0时，此时实际启用rediucer的数量会等于桶的个数i。
***