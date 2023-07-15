#  HIVE中表增加修改列属性

## 总结
+ (1) 使用 ALTER TABLE 各项命令修改

### （一）背景
为已存在的，且有历史数据的分区HIVE表增加列，或者修改列名之类的需求，因为要保留历史数据，所以不能删了表重建，此时就要修改表结构。


### （二）重命名HIVE表名
对HIVE表改名
```sql
ALTER TABLE table_name RENAME TO new_table_name;
```
这个命令可以让用户为表更名。数据所在的位置和分区名并不改变。换而言之，老的表名并未“释放”，对老表的更改会改变新表的数据。

### （三）改变表列名/类型
修改列名和列类型
```sql
ALTER TABLE table_name CHANGE
[CLOUMN] col_old_name col_new_name column_type
```

### （四）给表增加列/替换列
```sql
ALTER TABLE table_name ADD|REPLACE COLUMNS (col_name data_type);
```
ADD COLUMNS允许用户在当前列的末尾，分区列之前添加新的列，REPLACE COLUMNS允许用户更新列，更新的过程是先删除当前的列，然后在加入新的列。
