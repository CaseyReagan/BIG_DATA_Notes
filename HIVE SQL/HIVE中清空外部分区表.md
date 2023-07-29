# Hive中建立外部分区表
本文借鉴内容：https://blog.csdn.net/zengxingyuluo/article/details/100559163

## 总结
如果想彻底清空外部表内容，除了要drop分区和表之外，一定记得去hdfs路径下将存储的数据手动删除掉。

### （一）分区表原理
见"HIVE中建立外部分区表"文档
***

### （二）删除外部分区表
1.删除分区

    alter table t_test_order drop partition(year='2018');

2.删除表

    drop table if exists t_test_order;

3.删除hdfs上的数据

    hadoop fs -rm -r /test/order/year=2018

   
要从HDFS中删除文件，可以使用以下命令：

    hadoop fs -rm -r /path_to_file/file_name

要从HDFS中删除文件夹，可以使用以下命令：

    hadoop fs -rm -r /folder_name