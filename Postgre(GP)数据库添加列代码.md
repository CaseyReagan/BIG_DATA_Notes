#  Postgre(GP)数据库添加列代码

## 总结
+ (1)用ALTER TABLE  ADD COLUMN 命令
+ (2)GP外部表不要添加或者删除列，要修改表结构就删了重建

### （一）背景
为GP数据库表添加字段。

### （二）PostgreSQL ADD COLUMN 命令
+ Alter add column 命令的语法如下：
```sql
ALTER TABLE table_name  
ADD COLUMN new_column_name data_type;
```

+ 使用 alter table 命令添加多列
使用 alter table 命令添加多列的语法：
```sql
ALTER TABLE table_name  
ADD COLUMN new_column_name_1 data_type constraint,  
ADD COLUMN new_column_name_2 data_type constraint,  
ADD COLUMN new_column_name_N data_type constraint;
```

### （三）注意一个问题
因为GP数据的外部表，是通过gphdfs或者pxf方式直连hadoop数据库的文件系统，所以遇到HIVE表做了修改，而要增加字段到GP数据库内时，
GP内部表可以用ADD COLUMN。
GP外部表不可以直接添加列，因为列名列位置与HIVE表是一一对应的。