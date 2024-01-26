#  HIVE和Hadoop版本对应关系
本文借鉴：1.https://blog.51cto.com/u_16213330/8715294
2. 

## 总结
HIVE与Hadoop的版本在兼容性上有对应关系。

### （一）背景
Hive是一种基于Hadoop的数据仓库基础设施，用于提供数据查询和分析的高级接口。它允许用户使用类似于SQL的查询语言(HiveQL)来处理存储在Hadoop集群中的大规模数据集。
+ 关于HIVE于Hadoop的作用的简介看另一篇《HIVE于hadoop到底是什么的简介》   

Hadoop是一个开源的分布式计算平台，它提供了可靠性、可扩展性和容错性，适用于处理大规模数据集。Hive是在Hadoop之上构建的，通过将查询转换为适用于MapReduce任务的操作，实现了数据的处理和分析。   
在工作中，大数据的集群涉及到不同版本的HIVE和Hadoop，在使用新的3.0版本的HIVE时甚至出现与1.0版本HIVE的SQL代码不通用的情况，研究一下差异。   
***

### （二）HIVE和Hadoop查看版本的命令
查看Hadoop版本命令。这个集群的Hadoop版本为2.7.3
```sql
hadoop version

/*
Hadoop 2.7.3.2.6.1.0-129
Subversion git@github.com:hortonworks/hadoop.git -r 45e64533cdee3edf67c7b88a0267c64c194f93e5
Compiled by jenkins on 2017-05-31T03:06Z
Compiled with protoc 2.5.0
From source with checksum deba7ab784606611731cd7c37443e1c
This command was run using /data1/hdp-indata1.5/2.6.1.0-129/hadoop/hadoop-common.jar
*/
```
查看HIVE版本的命令。可以看到这个集群的HIVE版本是1.2.1
```sql
hive --version

/*
Hive 1.2.1000.2.6.1.0-129
Subversion git://c66-slave-38a0b1a6-3/grid/0/jenkins/workspace/HDP-parallel-centos6/SOURCES/hive -r 4c6995c87f30906625e01c28e5e111a53c2e582f
Compiled by jenkins on Wed May 31 03:19:31 UTC 2017
From source with checksum e3ee749ba0a797bb10a7e12265daa82d

*/
```

新集群的版本号。 Hadoop版本为3.3.1
```sql
hadoop version

/*
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/e3base/hadoop-3.3.1-e3base5.6.0-x86/share/hadoop/common/lib/slf4j-log4j12-1.7.30.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/e3base/hive-3.1.2-e3base5.6.0-x86/tez/lib/slf4j-log4j12-1.7.30.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
Hadoop 3.3.1-e3base5.6.0
Source code repository http://git.si-tech.com.cn:9002/SITECH-E3BASE/E3BASEV5.6.0/E3BASEV5.6.0/hadoop-3.3.1-e3base5.6.0.git -r c4001d68f11b118517c336da6c6d09e41db4dc72
Compiled by e3base on 2021-12-24T01:52Z
Compiled with protoc 3.7.1
From source with checksum 88a4ddb2299aca054416d6b7f81ca55
This command was run using /e3base/hadoop-3.3.1-e3base5.6.0-x86/share/hadoop/common/hadoop-common-3.3.1-e3base5.6.0.jar
*/
```

新集群的版本号。 HIve版本为3.1.2
```sql
hive --version

/*
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/e3base/hive-3.1.2-e3base5.6.0-x86/tez/lib/slf4j-log4j12-1.7.30.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/e3base/hadoop-3.3.1-e3base5.6.0-x86/share/hadoop/common/lib/slf4j-log4j12-1.7.30.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/e3base/hive-3.1.2-e3base5.6.0-x86/lib/log4j-slf4j-impl-2.17.0.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/e3base/hive-3.1.2-e3base5.6.0-x86/tez/lib/slf4j-log4j12-1.7.30.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/e3base/hadoop-3.3.1-e3base5.6.0-x86/share/hadoop/common/lib/slf4j-log4j12-1.7.30.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.apache.logging.slf4j.Log4jLoggerFactory]
Hive 3.1.2-e3base5.6.0
Git git://kf-app1/chunk1/e3base560/hive-3.1.2-e3base5.6.0 -r 2a153f60da41aa216e630c9c6d624071a5bd8774
Compiled by e3base on Fri Dec 24 14:08:03 CST 2021
*/
```
+ 可以看到旧环境为HIVE 1.2搭载在Hadoop 2.7上，新版本环境是HIVE 3.1对应Hadoop 3.3
+ 注意，进入HIVE后使用 select version(); 命令查看HIVE版本在 HIVE 1.0上不支持，在HIVE3.0上可行。| 3.1.2-e3base5.6.0 r2a153f60da41aa216e630c9c6d624071a5bd8774 |
***

### （三）常见的Hive与Hadoop版本的兼容性示例
在实际运用中的时候，我们要注意HIVE和Hadoop的兼容性。
+ 对于Hive 0.13，它与Hadoop 1.x和Hadoop 2.x版本兼容。这意味着可以在Hadoop 1.x或Hadoop 2.x集群上运行Hive 0.13。
+ 对于Hive 1.2，它同样与Hadoop 1.x和Hadoop 2.x版本兼容。这意味着可以在Hadoop 1.x或Hadoop 2.x集群上运行Hive 1.2。
+ 对于Hive 2.3，它与Hadoop 2.x版本兼容。这意味着只能在Hadoop 2.x集群上运行Hive 2.3。
***

