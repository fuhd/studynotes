Cloudera和Cloudera文档集概述
================================================================================
Cloudera提供了一个可扩展的，灵活的集成平台，可以轻松管理企业中快速增长的数据量和各种数据。
Cloudera产品和解决方案使您能够部署和管理Apache Hadoop和相关项目，操作和分析数据，并保持数据安
全。

### 1.Cloudera提供以下产品和工具

#### CDH
Apache Hadoop的Cloudera发行版和其他相关的开源项目，包括Apache Impala和Cloudera Search。
CDH还提供安全性和与众多硬件和软件解决方案的集成。

#### Apache Impala
一款用于 **交互式分析** 和商业智能的大规模并行处理 **SQL引擎**。其高度优化的架构使其非常适合具
有连接，聚合和子查询的传统BI风格查询。它可以从各种来源查询Hadoop数据文件，包括由MapReduce作业
生成的或加载到 **Hive表** 的数据文件。YARN资源管理组件允许Impala与Impala SQL并发运行批处理
工作。您可以通过Cloudera Manager用户界面与其他Hadoop组件一起管理Impala，并通过Sentry授权框
架保护其数据安全。

#### Cloudera Search
实时访问存储在 **Hadoop** 和 **HBase** 中的数据。搜索提供了 **近乎实时** 的索引，批量索引，
全文探索等，以及简单的全文界面，**不需要SQL或编程技能**。Search完全集成在数据处理平台中，使用
CDH中包含的灵活，可扩展且强大的存储系统。这 **消除了跨基础架构移动大型数据集以执行业务任务的需要**。

#### Cloudera Manager
用于部署，管理，监视和诊断CDH部署问题的复杂应用程序。 Cloudera Manager提供了 **管理控制台**，
这是一个 **基于Web的用户界面**，可使您的企业数据的管理变得简单直接。它还包括Cloudera Manager
API，您可以使用它来获取集群运行状况信息和指标，以及配置Cloudera Manager。

#### Cloudera Navigator
**CDH平台的端到端数据管理和安全性管理**。Cloudera Navigator Data Management使管理员，数据
管理员和分析人员能够在Hadoop中探索大量的数据集合。Cloudera Navigator加密和简化加密密钥的存储
和管理。Cloudera Navigator中强大的审计，数据管理，沿袭管理，生命周期管理和加密密钥管理功能使企
业能够遵守严格的合规性和法规要求。

### 2.文档指南
| 指南 | 描述 |
| :------------- | :------------- |
| Cloudera发行说明 | 本指南包含安装人员和管理员的发布和下载信息。它包含发行说明以及有关版本和下载的信息。该指南还提供了一个发布矩阵，显示Cloudera Manager和CDH的哪个发行版本支持某个产品的主要版本和次要版本。|
| Cloudera QuickStart VM | 本指南介绍如何下载和使用QuickStart虚拟机（VM），它提供了尝试CDH，Cloudera Manager，Impala和Cloudera Search所需的一切 |
| Cloudera安装 | 本指南提供了安装Cloudera软件的说明。 |
| Cloudera升级概述 | 本主题概述了Cloudera Manager和CDH的升级过程。 |
| Cloudera管理 | 本指南介绍了如何配置和管理Cloudera部署。 管理员管理资源，可用性以及备份和恢复配置。 此外，本指南还介绍了如何实现高可用性，并讨论了集成。|
| Cloudera数据管理导航器 | 本指南将向您介绍如何使用Cloudera Navigator Data Management组件进行全面的数据治理，合规性，数据管理和其他数据管理任务。|
| Cloudera操作 | 本指南介绍如何监控Cloudera的运行状况并诊断问题。您可以获取指标和使用信息并查看处理活动。本指南还介绍如何检查日志和报告以排除群集配置和操作问题以及监视合规性。|
| Cloudera安全 | 本指南适用于希望使用数据加密，用户身份验证和授权技术来保护群集的系统管理员。它提供了有关设置各种Hadoop组件以实现最佳安全性的概念概述和操作方法信息，包括如何设置网关以限制访问。 本指南一般假定您具有Linux和系统管理实践的基本知识。|
| Apache Impala - 交互式SQL | 本指南介绍了Impala，其功能和优点以及它如何与CDH配合使用。本主题介绍Impala概念，介绍如何规划Impala部署，并为初次用户提供教程以及描述场景和特定功能的更高级教程。您还可以找到语言参考，性能调整，使用Impala shell的说明，故障排除信息和常见问题。|
| Cloudera Search指南 | 本指南介绍了如何配置和使用Cloudera Search。其中包括提取，转换和加载数据，建立高可用性和故障排除等主题。|
| Spark指南 | 本指南介绍Apache Spark，这是一个分布式计算框架，可为批处理和交互式处理提供高性能。本指南提供了Spark应用程序教程，如何开发和运行Spark应用程序，以及如何将Spark与其他Hadoop组件一起使用。|
