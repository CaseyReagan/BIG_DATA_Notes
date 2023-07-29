#  HIVE中Union和UnionALL

## 总结
+ （1）Union,自动去除重复行，同时按默认规则排序
+ （2）Union all，不去重，原始数据并集，不排序

### （一）背景
HIVE sql中有union 和 union all两种操作，有什么异同以及语法规则。


### （二）Union 和 Union all
+ （1）Union,自动去除重复行，同时按默认规则排
（2）Union all，不去重，原始数据并集，不排序
+ 这里需要特别注意，每个select语句返回的列的数量和名字必须一样，同时字段类型必须完全匹配，否则会抛出语法错误。（虽然有时字段名字不一样也可以做并集，但是容易出错还麻烦，字段改名而已，规范操作比较好）
```sql
select a,b,c from t1

union all

select a,b,c from t2
;
```