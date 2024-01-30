#  HIVE的重要版本和特性
本文借鉴：1. HIVE版本和代码的官网 https://github.com/apache/hive
2.https://blog.csdn.net/L13763338360/article/details/107015259?spm=1001.2101.3001.6650.13&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-13-107015259-blog-109359945.235%5Ev40%5Epc_relevant_anti_vip_base&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-13-107015259-blog-109359945.235%5Ev40%5Epc_relevant_anti_vip_base&utm_relevant_index=21

## 总结
HIVE 1.x 是最稳定的版本，但是已经很久没有更新维护。HIVE 2.x目前更新的最频繁，HIVE 3.0也在维护。

### （一）背景
在检查HIVE与Hadoop的对应关系时，看到一些关于HIVE版本特性的内容。
***

### （二）一些重要的HIVE版本
+ #### HIVE 0.8.x 和 HIVE 0.9.x
（1）新增TIMESTAMP datatype（2）支持CREATE OR REPLACE VIEW（3）支持NOT IN 和 NOT LIKE（4）Ctrl+c将会提交kill命令，kill掉当前运行的query job,并且不会退出hive cli（5）输出map数和reduce数（6）支持BETWEEN操作（7） PRINTF()函数（8）增加"INSERT OVERWRITE TABLE X PARTITION (a=b, c=d) IF NOT EXISTS ..."操作，如果分区存在，则不会动。

+ #### HIVE 0.10.0
重要的优化版本，（1）优化了queries执行效率，优化了mapreduce执行效率，优化了 yarn的support内容，优化了union执行效率。（2）可以撤回drop table的操作。（3）增加重要功能Show create table。（3）优化、增加了EXPLAIN的内容。

+ #### HIVE 0.11.0
（1）新增DECIMAL数据类型，因为会逐步更接近SQL语言。（2）新增RANK、LEAD/LAG、ROW_NUMBER、FIRST_VALUE、LAST_VALUE 等視窗函數。它還引入了帶有 PARTITION BY 和 ORDER BY 的聚合 OVER 函數

+ #### HIVE 0.12.0
（1）优化了各种读取，执行，处理速度，partitions部分速度快于之前10倍。（2）新增 VARCHAR 数据类型  DATE 数据类型。（3）新增HCatalog模式。为Hbase新增了很多接口。

+ #### HIVE 0.13.x
Hive 0.13.x版本是最早的Hive版本之一，它引入了许多重要的特性和改进，包括向量化查询执行、预编译查询、动态分区等。（1）IN NOT/IN支持子查询嵌套（2）新增 EXISTS 和NOT EXISTS。（3）新增 CHAR 数据类型。（4）支持非ASCII码作表名。

+ #### HIVE 1.2.x
Hive 1.2.x版本是Hive的一个重要的里程碑，它在性能和稳定性方面进行了许多改进。此版本引入了更高效的查询优化器和执行引擎，提高了查询的执行速度。
+ #### HIVE 2.3.x
Hive 2.3.x在性能、可靠性和功能方面都有显著的改进。此版本引入了更快的查询优化器和执行引擎，支持更复杂的查询语句，并提供了更好的故障恢复机制。

***

### （三）HIVE 3.0 的新特性
1. 执行引擎MR变更为TEZ   
Apache TEZ的核心思想是将Map和Reduce拆分成若干子过程，即Map被拆分成Input、Processor、Sort、Merge和Output， Reduce被拆分成Input、Shuffle、Sort、Merge、Processor和Output等，分解后可以灵活组合成一个大的DAG作业。
   
2. 批处理TEZ，实时查询LLAP
(这个不太懂)   

3. ACID大数据事务支持
hive3.0就支持ACID， 但是只有ORC的存储格式数据才能进行修改和删除操作。比如PARQUET只是拥有了ACID特性，但同样不能进行更新删除的操作。   

4. Beeline代替Hive Cli
Hive 3仅支持瘦客户端Beeline，用于从命令行运行查询和Hive管理命令。Beeline使用与HiveServer的JDBC连接来执行所有命令。在HiveServer中进行解析，编译和执行操作。Beeline支持与Hive CLI相同的命令行选项，但有一个例外：Hive Metastore配置更改。
+ Beeline只需维护JDBC客户端，而不是维护整个Hive代码库。
+ Beeline相对于Hive Clie降低启动开销，因为不涉及整个Hive代码库。
+ hive cli可以绕过认证和建权，安全性不够
   
5. 不再支持内嵌Metastore
这个详见《HIVE 3.0 不再内置metastore》
