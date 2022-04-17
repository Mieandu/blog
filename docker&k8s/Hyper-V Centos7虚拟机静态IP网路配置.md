# Hyper-V Centos7网络虚拟机网络配置

连接外网配置过程,参考文章：https://my.oschina.net/u/4352261/blog/3751409

按文章配置可能的问题：ssh连接慢，ping外网慢

解决：修改eth0网卡配置`DEFROUTE=fasle`
