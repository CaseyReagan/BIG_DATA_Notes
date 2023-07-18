#  GP外部表连接hdfs的两种方式

本文借鉴内容：(1)https://stackoverflow.com/questions/49868945/when-i-use-multi-table-greenplum-bulk-load-in-kettle-i-report-the-following-err. (2)https://blog.csdn.net/weixin_39168541/article/details/125226209 (3)https://www.656463.com/wenda/Greenplumngramsjjldqssj_286
+ (4)http://www.dbaselife.com/doc/1692/ (5)https://docs-cn.greenplum.org/v6/pxf/overview_pxf.html (6)

## 总结
+ (1) 遇到这个问题，与其查错，不如根据hdfs表重建GP外部表
+ (2) gphdfs方式外部表不能有分区字段，pxf是结构化读取，可以拿分区字段


### （一）背景
修改了HIVE表的逻辑，并且增加了几个字段之后，GP连接HIVE表的GP外部表报错，报错信息。
```
 ERROR:Greenplum Segment reject limit reached. Aborting operation. Last error was: missing data for column
```

### （二）问题分析解决阶段一，GP外部表分隔符写错
这段报错大部分的问题原因是因为字段不匹配。找问题要从多个角度去寻找。   
解决方法阶段一： 检查GP的外部表的字段和HIVE内hdfs表的字段数量是否一致，字段类型是否一致，如果以上都无问题，字段和类型都一一对应。   
那么这时可以检查我遇到的问题，检查一下GP4建立外部表的时候，分隔符是否写错，必须要和HIVE表中的分隔符一致。例如：
检查HIVE表的分隔符如下
```sql
ROW FORMAT DELIMITED
  FIELDS TERMINATED BY '|'
  NULL DEFINED AS ''
```
所以当在GP4里用gphdfs连接HIVE表时分隔符要对应为：
```sql
FORMAT 'TEXT'  (DELIMITER  '|' NULL '')
```

### （三）问题分析解决阶段二，GP4的外部表连接hdfs方式不能带分区字段
debug之后发现报错依旧，只不过报错的 column 变为了最后一个分区字段tx_dt。   
经过查证，当GP外部表使用gphdfs方式直连hdfs的hive表的时候，是不能在GP外部表里带有HIVE表的分区字段的，因为在hdfs存储系统里，分区字段是按文件目录来实现的，并不是表的数据文件csv里的一个字段，所以GP外部表直连hdfs的时候，不能有分区字段。   

### （四）GP4和GP5连接hdfs的差异，gphdfs和pxf
当GP数据库升级到GP5.0之后，采取了新的连接hdfs表的办法。Greenplum平台扩展框架(PXF)，Greenplum平台扩展框架(PXF) 通过内置连接器提供跨异构数据源的并行、高吞吐量的数据访问和联合跨异构数据源的查询，该连接器将Greenplum数据库外部表定义映射到外部数据源。PXF源于Apache HAWQ项目。   
PXF和GPHDFS。虽然二者都利用了GPDB的外部表功能，但是前者需要额外安装部署PXF服务器进程，在复杂的IT环境中流程繁琐、极易出错，终端用户体验不佳。GPHDFS通过增加一种访问HDFS的外部表协议，让各个计算节点直连HDFS集群，不通过任何中间节点或者系统。   

### （五）gphdfs和pxf的使用差异
gphdfs是hdfs文件映射，pxf这里是表映射
pxf是结构化读取表结构，需要额外的部署。gphdfs是文件系统映射，所以不能直接映射分区字段。   