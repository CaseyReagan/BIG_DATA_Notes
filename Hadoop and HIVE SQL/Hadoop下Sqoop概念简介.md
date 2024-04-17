#  Hadoop下Sqoop概念简介
本文借鉴：1.https://blog.csdn.net/2301_76522810/article/details/131614232

## 总结

### （一）介绍
Sqoop是用在hadoop和“关系型数据库”服务器之间传送数据的工具。

+ 导入数据：MySQL、Oracle导入数据到hadoop的hdfs、hive、hbase等数据存储系统。
+ 导出数据：从hadoop的文件系统中导出数据到关系型数据库中。
***

### （二）导入导出的流程原理
导入数据入Hadoop流程：

1. 首先通过jdbc读取"关系型数据库"元数据信息，获取到表结构。
2. 根据元数据信息生成Java类。
3. 启动import程序，通过jdbc读取关系型数据库数据，并通过上一步的Java类进行序列化。
4. MapReduce并行写数据到Hadoop中，并使用Java类进行反序列化。

导出数据流程：

1. sqoop通过jdbc读取关系型数据库元数据，获取到表结构信息，生成Java类。
2. MapReduce并行读取hdfs数据，并且通过Java类进行序列化。
3. export程序启动，通过Java类反序列化，同时启动多个map，通过jdbc将数据写入到关系型数据库中。
***

### （三）Sqoop常用命令
| 命令名称 | 对应类 | 命令说明 |
|-------|-------|-------|
| import | ImportTool| 将关系型数据库数据导入到HDFS、HIVE、HBASE |
| export | ExportTool| 将HDFS上的数据导出到关系型数据库 |
| codegen | CodeGenTool| 获取数据库中某张表数据生成Java并打成Jar包 |
| create-hive-table | CreateHiveTableTool| 创建hive的表 |
| eval | EvalSqlTool| 查看SQL的执行结果 |
| list-databases | ListDatabasesTool| 列出所有数据库 |
| list-tables | ListTablesTool| 列出某个数据库下的所有表 |
| help | HelpTool| 打印sqoop帮助信息 |
| version | VersionTool| 打印sqoop版本信息 |
   
***

### （四）Sqoop链接参数
| Argument | Description |
|-------|-------|
| --connect <jdbc-uri> | Specify JDBC connect string 指定JDBC连接字符串|
| --connection-manager <class-name> | Specify connection manager class to use 指定要使用的连接管理器类|
| --driver <class-name> | Manually specify JDBC driver class to use 指定要使用的JDBC驱动类|
| --hadoop-mapred-home <dir> | Override $HADOOP_MAPRED_HOME 指定$HADOOP_MAPRED_HOME路径|
| --help | Print usage instructions 帮助信息|
| --password-file | Set path for a file containing the authentication password 设置用于存放认证的密码信息文件的路径|
| -P | Read password from console 从控制台读取输入的密码|
| --password <password> | Set authentication password 设置认证密码|
| --username <username> | Set authentication username 设置认证用户名|
| --verbose | Print more information while working 打印运行信息|
| --connection-param-file <filename> | Optional properties file that provides connection parameters 指定存储数据库连接参数的属性文件|

***


### （五）使用实例与参考项目
一个链接Mysql数据库的例子。

```sql
# 查询数据库列表 对标show databases
sqoop list-databases --connect jdbc:mysql://localhost:3306/ --username root --password 12345678
```

做了大量的Sqoop相关的项目，可以参考最全面也遇到问题最多的"SQOOP import to hive to SFTP"项目。   


	
	
	
