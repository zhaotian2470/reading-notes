# 解决问题

## 数据记录较多
如果数据记录较多，占用太多硬盘，则需要及时删除，具体方法如下：
* 找到配置文件broker.conf，修改数据记录相关的配置，例如：
deleteWhen = 04
fileReservedTime = 24
diskMaxUsedSpaceRatio = 70
* 重新启动rocketmq
