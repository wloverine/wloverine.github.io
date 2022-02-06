---
title: 记一次spark大表join问题
date: 2019-08-23 15:58:54
categories: 
- code
tags:
- Spark
- Hive
---
## 业务情景
- 两表根据一个key进行连接，一张表从hive中读取，数据量4千万，一张表从mongo中读取，数据量87万。起初，把小表直接进行广播变量，然后在大表的map算子里面根据key一条一条连接，这样速度很快。
-  后来小表数据源改变了，数据量上升到1千万，此时再用之前的方式去跑spark任务，效率低了很多。然后我把两个数据源的表都注册成临时表，写sparksql作连接，想看看这样和我之前的写法时间上差多少，结果，数据能成功跑完，但是spark任务在结束之后，报了错误：
```
19/08/22 15:40:22 ERROR client.TransportResponseHandler: Still have 1 requests outstanding when connection from /192.168.2.12:56274 is closed
19/08/22 15:40:22 ERROR client.TransportClient: Failed to send RPC 4801162101817174823 to /192.168.2.14:43434: java.nio.channels.ClosedChannelException
java.nio.channels.ClosedChannelException
	at io.netty.channel.AbstractChannel$AbstractUnsafe.write(...)(Unknown Source)
19/08/22 15:40:22 INFO storage.BlockManagerInfo: Removed broadcast_8_piece0 on s2:39066 in memory (size: 809.3 KB, free: 5.2 GB)
19/08/22 15:40:22 WARN storage.BlockManagerMaster: Failed to remove broadcast 8 with removeFromMaster = true - Failed to send RPC 8112168140978160966 to /192.168.2.15:58680: java.nio.channels.ClosedChannelException
java.io.IOException: Failed to send RPC 8112168140978160966 to /192.168.2.15:58680: java.nio.channels.ClosedChannelException
```
此时的spark任务已经结束，状态为success，并且最终join后的数据也成功入库了。
<!--more -->
- 网上大部分的解决方案都是修改yarn-site.xml文件的两个参数，但是并没有奏效：
```
<property>
    <name>yarn.nodemanager.pmem-check-enabled</name>
    <value>false</value>
  </property>
  <property>
    <name>yarn.nodemanager.vmem-check-enabled</name>
    <value>false</value>
  </property>
```
## 问题修复
经过多次尝试，终于找到了两种方式可以解决此问题。
1. 依然沿用之前的广播变量模式，不过不同的是，这次直接在spark里面，利用spark2.2之后的hint特性，显示指定join的方式:
```select  /*+ broadcast(table)/ from ...```
	这种方式会无视```spark.sql.autoBroadcastJoinThreshold	```默认的10M，对于两张表中较小的那张进行广播变量，如果广播变量超时了，记得将```spark.sql.broadcastTimeout```的时间设高一点，可以设为600，默认是300(单位是s)。这第一种方式可以成功解决上面的问题，并且spark任务的执行时间也要比自己手动广播变量进行map join要来的快(具体快的原因还不清楚，可能得益于sparksql的底层优化).
2. 第二种方法，我这里将大表进行了repartition,分区数从原先的8100换成了300，然后再正常进行join，也没有报错，并且运行时间也和第一种方式差不多。


## 总结
1. 本文的场景不是典型的大表join小表，更接近于大表join中表，两张表的数据量差异并不是特别大，而且广播变量的开销其实也不小，所以，在这种情况下，broadcast join和shuffle hash join的运行时间相差不大。
2. 这里留了个疑问，为什么第二种方法，我repartition之后，两表进行join，就不会报错，难道两个分区数量相差很大的表连接就会产生这种错误吗？