#  HIVE中的like和rlike以及通配符

## 总结
+ (1) 用LIKE要快一些

### （一）背景
有时候需要在HIVE里做模糊匹配，需要用到like和rlike还有通配符。   

### （二）like操作符
ike操作符是最常用的模式匹配操作符之一。它用于根据通配符模式从字符串中匹配出符合条件的数据。like操作符支持使用百分号（%）和下划线（_）作为通配符。其中，%表示任意长度的字符串，_表示一个字符。
```sql
SELECT * FROM table_name WHERE column_name LIKE 'abc%';
```

### （三）rlike操作符和regexp操作符
rlike操作符是正则表达式匹配操作符。它使用正则表达式对字符串进行匹配。
```sql
SELECT * FROM table_name WHERE column_name RLIKE '^a.*c$';
```
此外，还可以用 regexp 替换 rlike，用 not regexp 替换 not rlike ，效果一样。

### （四）常用正则表达式
```sql
\： 转义用，序列 \ 匹配

^： 匹配输入字符串开始的位置

$： 匹配输入字符串结尾的位置

*： 零次或多次匹配前面的字符或子表达式

?： 零次或一次匹配前面的字符或子表达式

x|y： 匹配 x 或 y

[xyz]： 字符集。匹配包含的任一字符

\w： 匹配任何字类字符，包括下划线

.： 匹配所有单个字符
```

### （五）使用多个LIKE运算符进行Hive查询
如果要查询多个like的内容那么该怎么写呢？

```sql
SELECT * 
FROM some_table
WHERE
some_col LIKE '%abc%'
OR
some_col LIKE '%xyz%'
OR
some_col LIKE '%pqr%'
OR
... (some more LIKE statements)
```
或者用rlike的正则表达式
```sql
WHERE some_col RLIKE 'abc|pqr|xyz' 
```


