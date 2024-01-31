#  HIVE 3.0 不再内置metastore
本文借鉴：1. 

## 总结

### （一）背景
在学习使用新环境HIVE 3.0的时候，发现不再内置metastore。
***

### （二）HIVE 3.0 新特性不再内置Metastore
HIVE 3.0 版本不再支持内嵌的 Metastore（元数据存储）模式。在早期的 HIVE 版本中，可以通过配置使用内嵌的 Derby 数据库作为 Metastore。但从 HIVE 3.0 开始，推荐使用外部的 MySQL 或 PostgreSQL 数据库来存储元数据。   
可以通过配置 hive-site.xml 文件，指定外部数据库的连接信息。示例配置（hive-site.xml）：
```Xml
<configuration>
  <property>
    <name>hive.metastore.uris</name>
    <value>thrift://your-metastore-host:9083</value>
  </property>
 
  <property>
    <name>hive.metastore.warehouse.dir</name>
    <value>/user/hive/warehouse</value>
  </property>
 
  <!-- 其他配置... -->
</configuration>
<!-- 确保替换 your-metastore-host 为实际的 Metastore 服务器地址，以及根据实际情况配置端口和数据仓库目录 -->
```
***

### （三）实际内容示例
在我们最新的HIVE 3.0的环境下，先找到配置文件hive-site.xml的地址，然后获取读该文件的权限
```Bash
cat /xxxxxx/hive/conf/hive-site.xml
```
打印内容后，找到Metastore配置相关的部分。
```Xml
<property>
      <name>hive.metastore.uris</name>
      <value>thrift://mxxx-xxx-xx-sinn01.xxx.hk.xxxxxxxxx.com:15001,thrift://mxxx-xxx-xx-sinn02.xxx.hk.xxxxxxxxx.com:15001</value>
    </property>

    <property>
      <name>hive.metastore.warehouse.dir</name>
      <value>/apps/hive/warehouse</value>
    </property>
```
此处可以看到我们没有将Metastore设置为外部服务器，而是分了两台hadoop的机器，相当于手动内嵌了Metastore。
两台机地址是为了符合，服务器主备的HA策略。
hive-site.xml文件具体内容见本地文件《hive-site.xml.txt》
***

