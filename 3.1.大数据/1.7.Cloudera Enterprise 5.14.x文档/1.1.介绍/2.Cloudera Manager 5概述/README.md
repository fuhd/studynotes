Cloudera Manager 5概述
================================================================================
**Cloudera Manager是管理CDH群集的端到端应用程序**。Cloudera Manager通过对CDH集群的每个部分提供
细粒度的可见性和控制权，为企业部署制定了标准，从而提高性能，提高服务质量，提高合规性并降低管理成本。借助
Cloudera Manager，您可以轻松部署和集中操作完整的CDH堆栈和其他托管服务。该应用程序可自动执行安装过程，
**将部署时间从几周缩短到几分钟**；为您提供集群范围的实时主机和服务运行视图；**提供了一个单一的中央控制台
来执行群集中的配置更改**；并整合了各种报告和诊断工具来帮助您优化性能和利用率。本入门介绍了Cloudera
Manager的基本概念，结构和功能。

### 1.术语
要有效地使用Cloudera Manager，您应该首先了解其术语。 术语之间的关系如下所示，其定义如下：

![术语](img/1.jpg)

一些术语，如 **集群** 和 **服务**，将被使用而无需进一步解释。其他部分，如 **角色组，网关，主机模板和
parcel** 将在后面的章节中扩展。

#### 1.1.部署
Cloudera Manager的配置及其管理的所有群集。

#### 1.2.动态资源池
在Cloudera Manager中，资源的命名配置和用于在池中运行的YARN应用程序或Impala查询之间调度资源的
策略。

#### 1.3.集群
+ 一组包含HDFS文件系统并在该数据上运行MapReduce和其他进程的计算机或机架。伪分布式群集是在单台机器
上运行的CDH安装，可用于演示和个人学习。
+ 在Cloudera Manager中，这是一个逻辑实体，它包含一组主机，安装在主机上的单一版本的CDH以及在主机上
运行的服务和角色实例。主机只能属于一个群集。Cloudera Manager可以管理多个CDH群集，但每个群集只能与
单个Cloudera Manager Server或Cloudera Manager HA配对关联。

#### 1.4.主机
在Cloudera Manager中，运行角色实例的物理机器或虚拟机器。主机只能属于一个群集。

#### 1.5.机架
在Cloudera Manager中，物理实体包含一组通常由相同交换机提供服务的物理主机。

#### 1.6.服务
+ 尽可能在可预测的环境下，在 **/etc/init.d/** 中运行System V init脚本的Linux命令，删除大多数
环境变量并将当前工作目录设置为/。
+ Cloudera Manager中的一类管理功能，可以分发或不分发，在群集中运行。有时称为服务类型。例如：
MapReduce，HDFS，YARN，Spark和Accumulo。在传统环境中，多个服务在一台主机上运行; 在分布式系统中，
服务在许多主机上运行。

#### 1.7.服务实例
在Cloudera Manager中，一个在集群上运行的服务实例。例如：“HDFS-1”和“yarn”。**服务实例跨越许多角
色实例**。

#### 1.8.角色
在Cloudera Manager中，服务中的一类功能。例如，**HDFS服务** 具有以下 **角色**：NameNode，
SecondaryNameNode，DataNode和Balancer。有时称为 **角色类型** 另请参阅用户角色。

#### 1.9.角色实例
在Cloudera Manager中，在主机上运行角色的实例。它通常映射到一个Unix进程。例如：“NameNode-h1”和
“DataNode-h1”。

#### 1.10.角色组
在Cloudera Manager中，一组角色实例的配置属性。

#### 1.11.主机模板
Cloudera Manager中的 **一组角色组**。将模板应用于主机时，将创建每个角色组的角色实例并将其分配给该
主机。

#### 1.12.网关
**通常为特定集群服务提供客户端访问权限的一种角色**。例如，HDFS，Hive，Kafka，MapReduce，Solr和
Spark各自都具有网关角色，**为其客户提供访问其各自服务的权限**。网关角色的名称并不总是具有“网关”，
也不是专门用于客户端访问。例如，Hue Kerberos Ticket Renewer是一个代理Kerberos票据的网关角色。

**支持一个或多个网关角色的节点有时被称为网关节点或边缘节点**，其中“边缘”的概念在网络或云环境中是常见的。
就Cloudera群集而言，当从Cloudera Manager管理控制台的“操作”菜单中选择“部署客户端配置”时，群集中的
网关节点将接收相应的客户端配置文件。

#### 1.13.parcel
二进制分发格式，包含已编译的代码和元信息，如软件包描述，版本和依赖关系。

#### 1.14.静态服务池
在Cloudera Manager中，**总集群资源（CPU，内存和I/O权重）的静态分区** - 跨越一系列服务。

#### 1.15.群集示例
请考虑具有四台主机的群集"Cluster1"，如以下Cloudera Manager列表中所示：

![集群1](img/2.png)

主机tcdn501-1是群集的“master”主机，因此与其他主机上运行的7个角色实例相比，它拥有更多的角色实例21。
除CDH“master”角色实例外，tcdn501-1还具有Cloudera管理服务角色：

![角色实例](img/3.png)

### 2.架构
如下所示，Cloudera Manager的核心是Cloudera Manager Server。服务器承载管理控制台Web服务器和应用
程序逻辑，负责安装软件，配置，启动和停止服务以及管理运行服务的群集。

![架构](img/4.png)

Cloudera Manager Server与其他几个组件一起工作：
+ **代理** - 安装在每台主机上。代理负责启动和停止进程，解压缩配置，触发安装以及监视主机。
+ **管理服务** - 由一组执行各种监视，警报和报告功能的角色组成的服务。
+ **数据库** - 存储配置和监视信息。通常，多个逻辑数据库在一个或多个数据库服务器上运行。例如，
Cloudera Manager Server和监控角色使用不同的逻辑数据库。
+ **Cloudera存储库** - 由Cloudera Manager分发的软件存储库。
+ **客户端** - 是与服务器交互的接口：
  - **管理控制台** - 管理员用于管理集群和Cloudera Manager的基于Web的用户界面。
  - **API** - 与开发人员创建自定义Cloudera Manager应用程序的API。

#### 2.1.心跳
心跳是Cloudera Manager中的主要通信机制。默认情况下，代理 **每15秒** 向Cloudera Manager服务器发送
检测信号。但是，为了减少用户等待时间，当状态发生变化时，频率会增加。

在心跳交换过程中，代理通知Cloudera Manager Server其活动。 而Cloudera Manager Server则会响应代理
应执行的操作。代理和Cloudera Manager Server最终都会进行一些调整。例如，如果您启动服务，代理将尝试启动
相关流程; 如果某个进程无法启动，Cloudera Manager Server会将启动命令标记为失败。

### 3.状态管理
Cloudera Manager Server维护集群的状态。此状态可以分为两类：“**模型**”和“**运行时**”，两者均存储在
Cloudera Manager Server数据库中。

![状态管理](img/5.png)


































dd
