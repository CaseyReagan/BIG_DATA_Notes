# HIVE报错DEPRECATED：Use of this script to execute hdfs command is deprecated。
本文借鉴内容：https://blog.csdn.net/zhao2chen3/article/details/109367003

## 总结
+ 版本问题而已，hadoop命令改为hdfs命令


### （一）背景
执行hadoop命令出现报错，DEPRECATED: Use of this script to execute hdfs command is deprecated.Instead use the hdfs command for it.
***

### （二）解决
原来从0.21.0版本以后，hadoop 命令换成了hdfs命令，上面的命令如同下面的命令：
```
 hadoop fs -ls  //报错
 hdfs dfs -ls  // 这时就没有错误了
```
实际上hadoop命令虽然告警，但是其实依然执行成功了。