# SQLserver中字符串函数
本文借鉴：https://blog.csdn.net/weixin_43960383/article/details/124578348

### （一）背景
在修改一个SQL server的SQL代码时，遇到了很多字符串处理函数。总结一下如下。


| 目录 | 内容 |
|------+------|
| Left | 返回字符串从左边开始指定个数的字符 |


### （二）Left 和 Right 函数
Left 函数用来返回字符串中从左开始指定个数的字符；
Left 函数的语法格式为：
```sql
Left(character_expression,integer_expression)
```

Right 函数用于返回字符串中从右边开始指定个数字符；
Right 函数的语法格式为：
```sql
Right(character_expression,integer_expression)
```

### （三）Len 函数
Len 函数返回指定字符串表达式的字符数，其中不包括含尾随空格；
```sql
Len(string_expression)
```

### （四）Substring 函数
Substing 字符串截取函数   
Substing 函数的语法格式为：
```sql
select SUBSTRING('I am Teacher!',3,4)

--am T
```

### （五）Str 函数
Str 函数将数字数据转换为字符数据；
Str 函数的语法格式为：
```sql
Str(float_expression)
```

### （六）Reverse 函数
Reverse 函数返回字符串的逆序；
Reverse 函数的语法格式为：
```sql
Reverse(string_expression)
```

### （七）Replace 函数
Replace 函数用于使用另一个字符串替换指定字符串；
Replace 函数的语法格式为：
```sql
Replace(string_expression,string_pattern,string_replacement)

```

### （七）LOWER()和UPPER() 函数

LOWER()将字符串全部转为小写；UPPER()将字符串全部转为大写。
