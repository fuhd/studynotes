Cloudera安装配置实例（CDH5.14.2）
=================================================================================
本实例主要记录 **CentOS7.4** 上离线安装CDH和Cloudera Manager。安装的版本为 **Cloudera Manager 5.14.2**，
如遇不同版本，安装方式大致相同。

| 内容 | 版本 |
| :----| :---|
| CentOS | 7.4 64位 |
| JDK | 1.7 |
| Cloudera Manager | 5.14.2 |

## cloudera CDH中需要安装的组件

### 必需安装的组件
+ HDFS                                          
+ YARN                                          
+ MapReduce                                    
+ ZooKeeper                                     
+ Hive                                          
+ Hue                                           
+ Oozie                                         
+ Sqoop1                                        
+ Impala                                        

### 按需安装的组件
+ Spark
+ HBase
+ Flume
+ Kafka
+ Pig
+ solr clouderasearch
