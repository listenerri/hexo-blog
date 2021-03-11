---
title: Clickhouse 数据副本与集群高可用
date: 2021-03-11 13:36:17
categories:
- clickhouse
tags:
- cluster
- replica
- 集群
- 高可用
- 副本
---

使用的 CH 版本：19.16.9.37

官方的文档看得晕头转向，可能还是自己英文功底不够，在读完官方的相关文档后，自己又做了大量测试，总结一下心得。

推荐阅读的官方文档：
- https://clickhouse.tech/docs/en/engines/table-engines/mergetree-family/replication
- https://clickhouse.tech/docs/en/getting-started/tutorial/#cluster-deployment
- https://clickhouse.tech/docs/en/engines/table-engines/special/distributed

如果看完本文仍有疑惑的地方，建议阅读下官方文档。

在 CH 中单独提及“副本”涉及到两个概念，副本表和副本分片，顾名思义，一个是为表做副本，一个是为分片做副本。

为了避免混乱后面会分开说这两种副本。


## 副本表（replicated table）

副本表比较容易理解，所有的副本表之间会相互同步数据，注意是相互同步。

副本表从使用角度来看并没有主副之分，任何一个副本表都可以拿来正常使用，
因此在创建副本表时，即便是第一个副本表也需要用 Replicated* 系列引擎创建。

副本表依赖 zookeeper，但并不依赖集群。

我想再解释一下“副本表并不依赖集群”这句话：

首先，在多个实例上创建副本表时，副本表并不要求这些实例们必须组成集群，也就是说即便是两个毫无关系的 CH 实例，
只要他们之间可以通过网络互相访问，就可以在这两个实例上创建副本表，当然如果组成集群也是可以的；
其次，多个副本表也可以在单个 CH 中也可以创建出来，但个人认为这其实没有太大的意义，
因为这种模式下，副本数据的安全性在发生硬盘物理损坏时得不到保障。

创建副本表时，引擎函数最少需要两个参数，其他参数可以继续在括号内顺序填写：
- zookeeper_path
    在 zookeeper 中创建的路径，用来标识一组副本表，同一组的副本表存储的都是相同的数据，它们之间互为副本，
    同一组副本表在不同实例上创建时要使用相同的路径，示例：
    `/clickhouse/tables/replications/database_name/table_name`
- replica_name
    在 zookeeper 中 zookeeper_path/replicas 路径下注册节点，用来识别同一组内的副本表，
    组内每个副本表应该使用不同的 replica_name，建议使用 CH 实例服务器主机名或域名。

当 CH 实例比较多且实例间组成了集群时，可以使用 CH 的 DDL 特性，即 ON CLUSTER 语句，一次性在多个实例上创建副本表，
这种情况下，建表语句中的引擎函数就不能把 replica_name 参数写死了，官方推荐在配置文件中设置宏来进行自动替换。

此外，CH 服务的配置文件中有两个重要的配置项：
- interserver_http_host
- interserver_http_port
这两个参数用来设置副本表所在 CH 实例之间的通讯地址和端口，
各 CH 实例会将自己的地址注册到 zookeeper_path/replicas/replica_name/host 中，这样其他实例就知道怎么访问了。

如果 CH 实例是运行在 docker 容器内的，还需要注意以下两点：
- host 参数不能使用 0.0.0.0 或 127.0.0.1，需要使用明确的主机 IP，以确保其他实例可以正常访问
- port 参数设置的值需要在创建容器时增加对应的端口映射


### 副本表示例

这里使用两个 CH 实例进行搭建，二者可以互通，主机名分别为：DBServerA 和 DBServerB。
在 DBServerA 上部署并启动一个 Zookeeper 服务，然后再分别在两台机器上部署并启动两个 CH 服务。
启动前注意修改服务配置文件，将 zookeeper 配置好。

分别连接到两个 CH 实例，命令行或图形化客户端均可，在 DBServerA 上执行如下 SQL 创建第一个副本表：

```
CREATE TABLE default.test
(`data` Float64)
ENGINE = ReplicatedMergeTree('/clickhouse/tables/replications/default/test', 'DBServerA') 
ORDER BY `data`
SETTINGS index_granularity = 8192
```

在 DBServerB 上执行如下 SQL 创建第二个副本表：

```
CREATE TABLE default.test
(`data` Float64)
ENGINE = ReplicatedMergeTree('/clickhouse/tables/replications/default/test', 'DBServerB') 
ORDER BY `data`
SETTINGS index_granularity = 8192
```

可以看到上面两个 SQL 分别在两个 CH 实例上创建了两个同名的表：`test`，
使用了 `ReplicatedMergeTree` 引擎，引擎函数的第一个参数是相同的，用来表示这两个副本表是同一组的，存储的是相同的数据。
引擎函数的第二个参数是不同的，用来表示这个组内共有两个副本表。

在 DBServerA 上执行如下 SQL 插入一条数据：

```
insert into default.test
values
(6)
```

插入完成后可以在两个 CH 实例中执行 SELECT 语句查看，会发现 DBServerB 中也有了这一条值为 1 的数据。

也可以在 DBServerB 上继续执行插入语句，注意修改值 1 到其他值，会发现 DBServerA 也会自动同步过去。

关闭 DBServerA 的 CH 服务，继续在 DBServerB 上执行插入，修改，删除操作后，再启动 DBServerA 上的 CH 服务，
会发现 DBServerA 将自己同步到了与 DBServerB 相同的状态。

至此，同步表测试完成。


## 副本分片

副本分片（replica）依赖 zookeeper 和集群。

需要了解 CH 的集群知识才能理解副本分片，首先不考虑副本，我们先看看什么是分片？

先来看一段 CH 集群的最简配置，两台机器组成集群：

```
<remote_servers incl="clickhouse_remote_servers" >
    <my_test_cluster>
         <shard>
             <replica>
                 <host>192.168.1.101</host>
                 <port>9000</port>
             </replica>
         </shard>
         <shard>
             <replica>
                 <host>192.168.1.102</host>
                 <port>9000</port>
             </replica>
         </shard>
     </my_test_cluster>
</remote_servers>
```

上述配置创建了一个名为：`my_test_cluster` 的集群，整个集群的数据被拆分为两个分片，每个分片有一个 CH 节点，
之所以称之为分片，是从集群数据角度来看的，每个分片只包含整个集群的一部分数据，当需要查询整个集群的数据时，
需要使用到 CH 的分布式表引擎。

再来看上述配置，每一个 `shard` 小节就是一个分片，而 `shard` 小节内部的 `replica` 即为副本分片。

在这里明确强调一下两个概念：
- shard：分片，集群完整数据的一部分
- replica：副本分片，真正为分片存储数据的地方，当仅有一个 replica 时，shard 等价于 replica

分片的概念不能与 CH 实例或节点划等号的，分片只与数据有关，分片更像是节点的上一层概念。
有多少分片就意味着数据被拆分成了多少份，倒是副本分片的存在方式与节点差不多。
**<font color=red>
注意！一个分片下的 CH 实例是不可以在其他分片下再次出现的，否则在查询数据时就会出问题，
CH 有可能会重复查询出同一分片下的数据，我留意到国内有不少相关文档出现了此错误。
</font>**

只配置一个副本分片时 CH 集群就已经可以正常工作了，在通过分布式表引擎查询整个集群的数据时，
就会使用到所有节点的硬件资源，但是这种形式的集群可称之为高性能集群，但没有丝毫高可用性的保障，
因为任何一个节点掉线时，都会导致查询报错。

设置多个副本分片（replica）的目的就是为了提高可用性，其配置方法也很简单，
就是在一个 `shard` 小节内部配置多个 `replica` 小节即可，比如：

```
<remote_servers incl="clickhouse_remote_servers" >
    <my_test_cluster>
         <shard>
            <internal_replication>false</internal_replication>
             <replica>
                 <host>192.168.1.101</host>
                 <port>9000</port>
             </replica>
             <replica>
                 <host>192.168.1.103</host>
                 <port>9000</port>
             </replica>
         </shard>
         <shard>
            <internal_replication>false</internal_replication>
             <replica>
                 <host>192.168.1.102</host>
                 <port>9000</port>
             </replica>
             <replica>
                 <host>192.168.1.104</host>
                 <port>9000</port>
             </replica>
         </shard>
     </my_test_cluster>
</remote_servers>
```

当一个分片拥有了多个副本分片时，就相当于有了备用军。
在某个副本出现问题导致掉线后，分布式表引擎在查询这一分片的数据时，会自动到可用的副本上去查找数据。

但如果一个分片的所有副本都掉线的话，那集群也是不能工作的，可以将分片的副本实例部署在多地区多机房来尽量避免这种问题。


### 副本分片的数据同步

到这里看起来一切都很美好，我原以为设置完多个副本分片后，它们之间就会自动相互同步，
但其实副本分片与前面提到的副本表完全不同，它并不会自动将一个副本上的所有表同步到其他副本上。
副本分片只能保证明确指定的表之间的同步关系，其他表则不会同步。

要想保持副本分片中的指定表的数据是同步的，从操作方法来看有三种方法：
- 普通表 + 分布式引擎表写入能力 + `internal_replication(false)`
- 副本表 + 分布式引擎表写入能力 + `internal_replication(true)`
- 副本表

前两种方法都涉及到一个非常关键的配置项：`internal_replication`（上面多副本分片示例配置中有）。
要解释这个配置项需要先了解分布式引擎表，文章开头的链接中有官方文档，这里只笼统得提一下它的作用：

```
分布式引擎表提供了 CH 集群模式下的跨分片读写能力。
```

而这个配置项则控制了分布式引擎表的数据写入行为：
- 设置为 `false` 时，分布式引擎表会把分到一个分片下的数据，写入到所有副本分片上，`false` 也是默认值。
- 设置为 `true` 时，分布式引擎表只把分到一个分片下的数据，写入到发现的第一个可用副本分片上。

#### 同步方法 1（不推荐）

首先需要说明的是，第一种方法官方不推荐使用，因为这种方法 CH 不能保证副本分片的数据一致性，
多个副本分片里的数据随着时间的推移会出现数据不一致的情况。

虽然此方法不被推荐，但为了文章内容完整性，还是简单说一下用法。

其实有了前面的知识积累，用法已经很明朗了，核心就是让分布式表引擎在写数据时主动写多份。

1. 确保 `internal_replication` 选项设置为 `false`
2. 创建一个包含两个分片的集群，每个分片包含两个副本分片
3. 在四个节点上创建普通数据表：`test`，注意不能是副本表
4. 在四个节点上创建分布式引擎表：`test_all`，其读写上面 `test` 表
5. 在任一节点上往 `test_all` 表写入多次数据
6. 在四个节点上分别查询 `test` 表内容，查看属于同一分片的表内容是否相同
7. 不出意外应该是相同的，这表示这种方法已经走通了


#### 同步方法 2

这第二种方法的同步原理则是利用了副本表，CH 通过 zookeeper 可以保证数据的一致性。
上层应用将数据写入分布式引擎表，其根据配置将数据分为多个块，分别写入各个分片。
由于副本表已经提供了数据同步功能，因此需要将 `internal_replication` 设置为 `true`，
告诉分布式引擎一个分片的数据，只需要写完成一个分片副本即可。


#### 同步方法 3

方法 3 与 方法 2 核心相同，但不使用分布式引擎表的写能力，故而也不需要配置 `internal_replication`。

这种方法使用的场景是：集群分为多个分片，每个分片上的数据有着明确的界限，虽然可以最终聚合在一起查询，但数据的写入和存储都需要分开，说起来有点类似于分库分表的概念。

由于不使用分布式表引擎写能力，因此上层应用需要分别与不同分片上的节点建立数据库连接，将数据分别写入，
个人更推荐的方法是将上层应用根据业务或者说数据界限拆分成多个模块，分别对应多个分片，各自写各自的数据，
最后上层应用提供给使用者的查询接口则是用分布式表引擎来查询所有分片的数据，即整个集群的数据。


### 高可用集群示例

这里使用同步方法 2 举例高可用集群的搭建方法。
使用四个 CH 实例进行搭建，它们之间可以互通，它们的 IP 地址分别为：
- 192.168.1.101
- 192.168.1.102
- 192.168.1.103
- 192.168.1.104

后续使用 101 102 103 104 简称它们。

在 101 上部署并启动一个 Zookeeper 服务，然后再分别在四台机器上部署并启动 CH 服务。
启动前注意修改服务配置文件，有两点需要修改：
- zookeeper 配置项
- cluster 配置项，四台机器配置相同，内容如下：
```
<!--
方法 1 依赖 internal_replication 为 false
方法 2 依赖 internal_replication 为 true
方法 3 忽略 internal_replication 的配置
且方法 1 不推荐使用，那么综上条件，无论使用方法 2 还是 3，
都建议始终配置 internal_replication 为 true
-->
<remote_servers incl="clickhouse_remote_servers" >
    <my_test_cluster>
         <shard>
            <internal_replication>true</internal_replication>
             <replica>
                 <host>192.168.1.101</host>
                 <port>9000</port>
             </replica>
             <replica>
                 <host>192.168.1.103</host>
                 <port>9000</port>
             </replica>
         </shard>
         <shard>
            <internal_replication>true</internal_replication>
             <replica>
                 <host>192.168.1.102</host>
                 <port>9000</port>
             </replica>
             <replica>
                 <host>192.168.1.104</host>
                 <port>9000</port>
             </replica>
         </shard>
     </my_test_cluster>
</remote_servers>
```

上述配置设置了一个名为 `my_test_cluster` 的集群，整个集群中的数据被分为了两份，即共两个分片，每个分片内有两个副本分片。

```
                          -- 101
                         /
            -- shard01 --
           /             \
          /               -- 103
cluster --
          \               -- 102
           \             /
            -- shard02 --
                         \
                          -- 104
```

我们计划让每组副本分片内部节点上数据完全相同，所以要确保所有新建的数据表（不包括分布式引擎表）都是用副本表，
在这里我们认为 101 和 103 属于一组，组名为 shard01，而 102 和 104 属于另一组，组名为 shard02。

需要在 101 和 103 上创建同组副本表，在 102 和 104 上创建另一组副本表，控制是否是同一组的方法是建表时的引擎参数。

分别连接到四个 CH 实例，命令行或图形化客户端均可，在 101 节点上执行如下 SQL 创建一个副本表：

```
CREATE TABLE default.test
(`data` Float64)
ENGINE = ReplicatedMergeTree('/clickhouse/tables/shard01/default/test', '101') 
ORDER BY `data`
SETTINGS index_granularity = 8192
```

在 103 节点上执行如下 SQL 创建一个副本表：

```
CREATE TABLE default.test
(`data` Float64)
ENGINE = ReplicatedMergeTree('/clickhouse/tables/shard01/default/test', '103') 
ORDER BY `data`
SETTINGS index_granularity = 8192
```

在 102 节点上执行如下 SQL 创建一个副本表：

```
CREATE TABLE default.test
(`data` Float64)
ENGINE = ReplicatedMergeTree('/clickhouse/tables/shard02/default/test', '102') 
ORDER BY `data`
SETTINGS index_granularity = 8192
```

在 104 节点上执行如下 SQL 创建一个副本表：

```
CREATE TABLE default.test
(`data` Float64)
ENGINE = ReplicatedMergeTree('/clickhouse/tables/shard02/default/test', '104') 
ORDER BY `data`
SETTINGS index_granularity = 8192
```

可以看到上面 4 个 SQL 分别在 4 个节点上创建了 4 个同名的表：`test`，使用了 `ReplicatedMergeTree` 引擎，
其中 101 和 102 一组所以引擎函数的第一个参数是相同的，103 和 104 同理，引擎函数的第二个参数是不同的，用来标识出每一个副本表。

接着创建分布式引擎表用来查询整个集群的完整数据，在 4 个节点上都执行如下 SQL：

```
CREATE TABLE default.test_all AS default.test
ENGINE = Distributed(my_test_cluster, default, test, rand())
```

接着开始验证集群内同步和高可用能力，在 101 节点上执行如下 SQL 插入一条数据：

```
insert into default.test
values
(1)
```

在 102 节点上执行如下 SQL 插入一条数据：

```
insert into default.test
values
(2)
```

执行完成后可以在 4 个节点上查询 `test` 表，会发现除了 101 节点，103 中也有了这一条值为 1 的数据，但没有值为 2 的数据；而 102 和 104 上只有值为 2 的数据，这说明分片内副本表同步工作正常。

接着在任一节点上查询 `test_all` 表，会发现可以查出 1 和 2 两条数据，这说明集群跨分片读取功能正常。

然后执行如下 SQL 直接往 `test_all` 表插入数据，建议调整 SQL 中的值多次执行插入操作，因为一两次的插入操作可能都为分配到同一个分片上：

```
insert into default.test_all
values
(2)
```

每执行一次插入操作，就分别查询一次所有节点上的 `test` 表和 `test_all` 表来观察集群行为，会发现数据会被随即分配到两个分片上，分片内部的副本分片（副本表）则会始终保持同步。

关闭掉 101 节点，在剩余节点上查询 `test_all` 表发现集群工作正常，数据也没有缺失，接着关闭 103 节点，然后在剩余节点上查询 `test_all` 表发现会报错，说明如果一个分片上的所有分片副本都掉线的话，则集群瘫痪。

接着恢复 101 节点，然后再查询 `test_all` 表发现集群工作正常，说明集群可以自动重连掉线节点；在 101 节点上插入、删除、修改一些数据，再启动 103 节点，再查询 103 节点上的 `test` 表，发现在 103 掉线时组内 101 节点的操作都已经同步到了 103 上。

至此，高可用集群测试完成。


## 其它问题

上述方案仔细分析的话会发现只做到了集群内部的高可用，这对于上层应用来说还不够。

比如沿用上述高可用配置的例子，上层应用使用了 104 节点作为整个集群的外部出入口，只与 104 建立了连接，如果 104 节点掉线了的话，即便集群内部高可用优化的再好，上层应用也无法使用集群。

这种情况下如果不想改动上层应用，或者说想对上层应用屏蔽高可用实现细节，可以使用 `keepalived` 技术，为整个集群所有节点创建一个虚拟 IP，上层应用只与这个虚拟 IP 连接就可以了。关于 `keepalived` 的具体使用方法就不在这篇文章里展开了。

