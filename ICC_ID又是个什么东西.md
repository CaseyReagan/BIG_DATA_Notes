#  ICC_ID又是个什么东西
本文借鉴：1.https://wenku.baidu.com/view/fec2786e862458fb770bf78a6529647d27283466.html?_wkts_=1705288532598&bdQuery=iccid%E5%92%8Cimsi%E6%9C%89%E4%BB%80%E4%B9%88%E5%8C%BA%E5%88%AB&needWelcomeRecommand=1

## 总结
ICC ID就是SIM卡的序列号，只做物理标识，不入网验证，决定唯一的物理SIM卡，可伪造。

### （一）背景
预付项目中当大部分关键字段都是复用给不同声明周期的用户时，要找到一个唯一标识来识别用户。

### （二）ICC_ID是个啥
ICCID代表集成电路卡识别码（Integrated Circuit Card Identifier），是一串唯一标识符，用于区分不同的SIM卡。一个ICCID由20位数字组成，前面是一个固定的识别码，后面是唯一的序列号。   
"89852XX"开头的ICC ID为香港运营商，"8985212，8985213"是CMHK，"8986XX"是内地运营商的ICC ID。

### （三）ICC ID与IMSI的区别
IMSI是国际移动用户识别码,是区别移动用户的标志,诸存在SIM卡中,可用于区别移动用户的有效信息。
+ ICCID是由SIM卡厂商分配和编程的，而IMSI是由运营商分配给用户的。
+ ICCID是用于区分不同的SIM卡，而IMSI是用于区分移动用户。
+ ICCID只是用来区别SIM卡,不作接入网络的鉴权认证。而IMSI在接入网络的时候,会到运营商的服务器中进行验证。
+ ICCID可以伪造,可以用一张空白多号卡,写入IMSI和KI,只要是经过破解的IMSI和KI,就可以接入网络,而ICCID可以任王意20位数字。

### （四）ICC ID用例
苹果手机激活的时候要将ICC ID和IMSI一起发送给苹果公司验证，验证后会绑定手机，直到下一次刷机。