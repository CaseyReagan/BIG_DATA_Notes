# SQLserver中十进制十六进制转换

### （一）背景
SQL中有的数据要存储为十六进制数据，涉及到进制的转换。

### （二）内置函数 master.dbo.fn_varbintohexstr
master.dbo.fn_varbintohexstr()函数为系统内置函数，用来将十进制数据转换为十六进制。   

### （三）CONVERT函数
将整型数值转换为十六进制数据,将十进制数值10，100，16777215 转换为十六进制数据显示
```sql
SELECT CONVERT(VARBINARY(8), 10) --0x0000000A

SELECT CONVERT(VARBINARY(8), 100) --0x00000064

SELECT CONVERT(VARBINARY(8), 16777215) --0x00FFFFFF
```

还可以将十六进制数据转为十进制数据
```sql
SELECT CONVERT(INT, 0xFFFFFF) --16777215

SELECT CONVERT(INT, 0x0F) --15

SELECT CONVERT(INT, 0x0A) --10
```

### （四）FORMAT函数
```sql
select FORMAT(10,‘x2’) --0x0a

select FORMAT(10,‘X2’) --0x0A
```