# GreenPlum服务器中的权限管理
本文借鉴内容：1.https://blog.csdn.net/weixin_42658788/article/details/88040209


### （一）背景
在做预付费用户诈骗电话识别拦截项目的时候，将最后取得的数据给到下游部门有权限访问的GP5服务器中，发生对方无权限读取的情况，做了一下服务器权限管理。
***

### （二）greenplum用户权限管理
新建用户：
```sql
CREATE role irdw_user WITH LOGIN ENCRYPTED PASSWORD ‘******’;
```
赋予库、schema权限给用户：
```sql
GRANT ALL ON DATABASE irdw_xut TO irdw_user;
GRANT ALL ON SCHEMA “public” to irdw_user;
```

一条是给表权限，一条是给schema权限，光给表权限是不够的。
```sql
GRANT SELECT ON TABLE "******".ODM_SE_PDD_BLOCK_MSISDN TO nocdev;

GRANT ALL ON SCHEMA "******" to nocdev;
```
