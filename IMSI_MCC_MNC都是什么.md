# 国际移动用户识别码IMSI和MCC和MNC都是什么
本文借鉴内容：1.http://m.shiliuwang.cn/item/151141.html

## 总结
+

### （一）背景
国际移动用户识别码（IMSI：International Mobile Subscriber Identification Number）是区别移动用户的标志，储存在SIM卡中，可用于区别移动用户的有效信息。IMSI是15位的十进制数。其结构如下：MCC+MNC+MSIN
***

### （二) MCC 和 MNC
（1）MCC（Mobile Country Code，移动国家码）：MCC的资源由国际电信联盟（ITU）在全世界范围内统一分配和管理，唯一识别移动用户所属的国家，共3位，中国为460。   
（2）MNC（Mobile Network Code，移动网络号码）：用于识别移动用户所归属的移动通信网，2~3位。   
（3）MSIN（Mobile Subscriber Identification Number，移动用户识别号码）：用以识别某一移动通信网中的移动用户。共有10位，其结构如下：   
EF+M0M1M2M3+ABCD   
其中，EF由运营商分配；M0M1M2M3和MDN（Mobile Directory Number，移动用户号码簿号码）中的H0H1H2H3可存在对应关系；ABCD：四位，自由分配。   
***

### (三)与IMEI的区别
IMSI不同于手机设备的标识IMEI（International Mobile Equipment Identity，国际移动设备标识）：IMEI是与手机绑定的。IMSI是与SIM（Subscriber Identity Module，用户识别模块）或者USIM（Universal Subscriber Identity Module，全球用户身份模块）。   
   
IMSI和MSISDN都是用户标识，在不同的接口、不同的流程中需要使用不同的标识。在通信系统中MSISDN（Mobile Station International Subscriber Directory Number，移动台国际用户目录号）又称为手机号码。
***

### (四) MCC、MNC表，
见维基百科：https://en.wikipedia.org/wiki/Mobile_country_code
***