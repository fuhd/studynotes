定制安装解决方案
================================================================================
Cloudera拥有两种类型的软件存储库，可用于安装Cloudera Manager或 **CDH-parcel存储库** 和
**package存储库** 等产品。这些存储库在大多数情况下都是有效的解决方案，但有时需要定制安装解决方案。
使用Cloudera托管的软件存储库需要通过Internet进行客户端访问。典型的安装使用最新的可用软件。在某些
情况下，这些行为可能并不理想，例如：
+ 您需要安装较旧的产品版本。例如，在CDH群集中，所有主机都必须运行相同的CDH版本。完成初始安装后，
您可能需要添加主机。这可能会增加群集的大小以处理较大的任务或替换较旧的硬件。
+ 要安装Cloudera产品的主机未连接到Internet，因此无法访问Cloudera存储库。（对于parcels安装，
只有Cloudera Manager Server需要访问Internet，但对于package安装，所有群集主机都需要访问Cloudera存储库）。
大多数组织将其网络的一部分与外部访问分开。隔离网段可提高安全性，但会增加安装过程的复杂性。

在这两种情况下，使用内部存储库可以满足组织的需求，无论这意味着安装特定版本的Cloudera软件还是在未连接
Internet的主机上安装Cloudera软件。

## 1.Parcels介绍
Parcels是一种打包格式，有助于从Cloudera Manager中升级软件。您可以从Cloudera Manager中下载，分发
和激活一个新的软件版本。Cloudera Manager将parcel下载到本地目录。将parcel下载到Cloudera Manager Server
主机后，不再需要Internet连接来部署parcel。 有关parcel的详细信息，请参阅Parcels。

如果您的Cloudera Manager Server无法访问Internet，则可以获取所需的parcels文件并将其放入parcels存储库。
有关更多信息，请参阅使用 **内部parcels信息库**。

## 2.了解package管理
我不使有它，也不讲它了。。。。。。。。











































111
