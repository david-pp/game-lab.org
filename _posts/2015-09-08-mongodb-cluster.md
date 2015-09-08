---
layout: post
title: 高可用的Mongodb集群
category : 架构
tags : []
date: 2015/09/08  21:46  +0800
--- 

刚接触Mongodb，就要用到它的集群，只能硬着头皮短时间去看文档和尝试自行搭建。迁移历史数据更是让人恼火，近100G的数据文件，导入、清理垃圾数据执行的速度蜗牛一样的慢。趁着这个时间，把这几天关于Mongod集群相关的内容整理一下。大概介绍一下Mongodb集群的几种方式：Master-Slave、Relica Set、Sharding，并做简单的演示。


使用集群的目的就是提高可用性。高可用性H.A.（High Availability）指的是通过尽量缩短因日常维护操作（计划）和突发的系统崩溃（非计划）所导致的停机时间，以提高系统和应用的可用性。它与被认为是不间断操作的容错技术有所不同。HA系统是目前企业防止核心计算机系统因故障停机的最有效手段。

HA的三种工作方式：

- 主从方式 （非对称方式）

   工作原理：主机工作，备机处于监控准备状况；当主机宕机时，备机接管主机的一切工作，待主机恢复正常后，按使用者的设定以自动或手动方式将服务切换到主机上运行，数据的一致性通过共享存储系统解决。

- 双机双工方式（互备互援）

   工作原理：两台主机同时运行各自的服务工作且相互监测情况，当任一台主机宕机时，另一台主机立即接管它的一切工作，保证工作实时，应用服务系统的关键数据存放在共享存储系统中。

- 集群工作方式（多服务器互备方式）
  
  工作原理：多台主机一起工作，各自运行一个或几个服务，各为服务定义一个或多个备用主机，当某个主机故障时，运行在其上的服务就可以被其它主机接管

<!--more-->


### 主从架构（Master-Slave）

![Mater-Slaves](/assets/img/mongodb-master-slave.png)

主从架构一般用于备份或者做读写分离。由两种角色构成：

- 主(Master)

  可读可写，当数据有修改的时候，会将oplog同步到所有连接的salve上去。

- 从(Slave)

  只读不可写，自动从Master同步数据。


特别的，对于Mongodb来说，并不推荐使用Master-Slave架构，因为Master-Slave其中Master宕机后不能自动恢复，推荐使用Replica Set，后面会有介绍，除非Replica的节点数超过50，才需要使用Master-Slave架构，正常情况是不可能用那么多节点的。

还有一点，Master-Slave不支持链式结构，Slave只能直接连接Master。Redis的Master-Slave支持链式结构，Slave可以连接Slave，成为Slave的Slave。

下面演示一下搭建过程：

1>. 启动Master

``` bash
mongod --port 2000 --master --dbpath masterdb/
```

2>. 启动Slave

``` bash
mongod --port 2001 --slave --source 127.0.0.1:2000 --dbpath slavedb/
```

3>. 给Master里面导入数据，查看Master和Slave的数据。你会发现导入Master的数据同时也会在Slave中出现。

``` bash
mongoimport --port 2000 -d test -c dataset dataset.json
```

``` bash
mongo --port 2000 test
db.dataset.count()

> 25359
```

``` bash
mongo --port 2001 test
db.dataset.count()

> 25359
```

4>. 试一下Master和Slave的写操作。你会发现，只有Master才可以对数据进行修改，Slave修改时候会报错。

``` bash
mongo --port 2001 test
db.dataset.drop()

>  Error: drop failed: { "note" : "from execCommand", "ok" : 0, "errmsg" : "not master" }

mongoimport --port 2001 -d test -c dataset dataset.json

> Failed: error checking connected node type: no reachable servers

```


### 副本集架构（Replica Set）

### 数据分片架构（Sharding）

### 高可用的Mongodb集群

### 数据的备份和恢复

### 小结

### 参考资料

http://baike.baidu.com/view/2850255.htm
