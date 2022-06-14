# Hive中的NULL(空值)和''(空字符串)
## 总结
+ （1）HIVE 中的NULL值会按照\n来存储用来占位，所以导入HIVE的时候尽量字段不要留空，而是要写上null。   
+ （2）但是如果想要节约HIVE中的存储空间，那么可以将所有的NULL转化为空字符串，以节省磁盘空间。   
+ （3）用NVL或者Coalesce函数来处理null值数据。
### 一、hive里面的 null(空值) 和 ''(空字符串) 是两个不同的东西
1、如果查某个字段【 a 是否为空】应该这样写：

```sql
a is null
 
a is not null
```
2、如果查某个字段 【a 是否为''】应该这样写：

```sql
a = ''
 
a <>''
```
在hive的数据中，null值（空值）较为常见，比如字段没有值，就是所谓的null值（空值），而 ''（空字符串）多为人为给定的，不多见。

```sql
（1）不同数据类型对空值的存储
   (a)int与string类型数据存储，null默认存储为\N。
   (b)string类型的数据如果为""，存储则是""。
   (c)往int类型的字段插入数据""时，结果还是\N。
（2）不同数据类型，空值的查询
   (a)对于int可以使用is null来判断空。
   (b)对于string类型，条件is null查出来的是\N的数据；而条件= ''，查询出来的是""的数据。
```

### 二、【表】NULL值的处理
HIVE表中默认将NULL存为\N，可查看表的源文件（hadoop fs -cat或者hadoop fs -text），文件中存储大量\N，   
hive的NULL有时候是必须的：   
1）、hive中insert语句必须列数匹配，不支持不写入，没有值的列必须使用null占位。   
2）、hive表的数据文件中按分隔符区分各个列。空列会保存NULL（\n）来保留列位置。但外部表加载某些数据时如果列不够，如表13列，文件数据只有2列，则在表查询时表中的末尾剩余列无数据对应，自动显示为NULL。   
所以，NULL转化为空字符串，可以节省磁盘空间，实现方法有三种：   
1）建表时直接指定（两种方式）   
```sql
CREATE TABLE hive_tb (id int,name STRING)
PARTITIONED BY ( `day` string,`type` tinyint COMMENT '0 as bid, 1 as win, 2 as ck', `hour` tinyint)
ROW FORMAT SERDE ‘org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe’
WITH SERDEPROPERTIES (
    ‘field.delim’='/t’,
    ‘escape.delim’='//’,
    ‘serialization.null.format'=''
) STORED AS TEXTFILE;
```
或者通过ROW FORMAT DELIMITED NULL DEFINED AS ''    
```sql
CREATE TABLE hive_tb (id int,name STRING)
PARTITIONED BY ( `day` string,`type` tinyint COMMENT '0 as bid, 1 as win, 2 as ck', `hour` tinyint)
ROW FORMAT DELIMITED 
    NULL DEFINED AS '' 
STORED AS TEXTFILE;
```

2）修改已存在的表：   
```sql
alter table hive_tb set serdeproperties('serialization.null.format' = '');
```
也可以修改为其他的存储方式。   
```sql
(1) alter table name SET SERDEPROPERTIES('serialization.null.format' = '\N'); 
    则底层数据保存的是'\N',通过查询显示的是'NULL'
    这时如果查询为空值的字段可通过 语句：a is null 或者 a='\\N'
(2) alter tablename SET SERDEPROPERTIES('serialization.null.format' = 'NULL'); 
    则底层数据保存的是'NULL',通过查询显示的是'NULL'
    这时如果查询为空值的字段可通过 语句：a is null 或者 a='NULL'
```

## 三、【字段】null值处理——NVL、Coalesce函数
1、NVL函数   
NVL函数的格式如下：NVL(expr1,expr2)   
含义是：如果第一个参数为null（空值）那么显示第二个参数的值，如果第一个参数的值不为null（空值），则显示第一个参数本来的值。   
```sql
select nvl(null,3);    -- 结果：3
 
select nvl('',3);      -- 结果：''   (注意：在hive中展示位1，但实际为''，可用select nvl('',3)='';验证)
```

2、Coalesce函数
Coalese函数格式如下：Coalesce(expr1, expr2, expr3….. exprn)   
Coalese函数的作用和NVL函数有点相似，其优势是有更多的选项。表示可以指定多个表达式的占位符。所有表达式必须是相同类型，或者可以隐性转换为相同的类型
作用是：返回表达式中第一个非空表达式。   

```sql
SELECT COALESCE(NULL,null,3,4,5); 　　-- 结果为：3
 
SELECT COALESCE(NULL,null,'',3,4,5);   -- 结果为：''
 
SELECT COALESCE(NULL,null,null,NULL);  -- 结果为：null
```