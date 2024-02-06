#  HIVE 3.0 的配置文件hive-site.xml
本文借鉴：1. 

## 总结

### （一）背景
HIVE 3.0的环境配置与查看，都在hive-site.xml文件之中，与之前的hive set命令不同。
***

### （二）如何查看hive-site.xml
在Hive 3中，hive-site.xml文件通常位于Hive的配置目录下。假设你的Hive配置目录是/etc/hive/conf，你可以使用以下命令在Linux系统中查看hive-site.xml文件的内容：
```Bash
cat /etc/hive/conf/hive-site.xml

less /etc/hive/conf/hive-site.xml
## 使用less或more命令逐页查看
```
如果你需要编辑这个文件，你需要具有相应的文件系统权限。可以使用vi，nano或其他编辑器。不建议如此做。
***

### （三）使用示例
用hive-site.xml检查新环境的metastore配置地址，详见《HIVE 3.0 不再内置metastore》
hive-site.xml文件内容详情，见本地文件《hive-site.xml.txt》
***
