Cloudera安装配置实例（CDH5.13.0）
================================================================================
主要用于数据中台适配CDH5.13.0。

## Cloudera Manager(CDH5)内部结构
+ `/var/log/*`：相关日志文件（相关服务的及CM的）；
+ `/usr/share/cmf/`：程序安装目录；
+ `/usr/lib64/cmf/`：Agent程序代码；
+ `/var/lib/cloudera-scm-server-db/data`：内嵌数据库目录；
+ `/usr/bin/postgres`：内嵌数据库程序；
+ `/etc/hadoop/conf`：Hadoop配置文件目录；
+ `/etc/cloudera-scm-agent/`：agent的配置目录；
+ `/etc/cloudera-scm-server/`：server的配置目录；
+ `/opt/cloudera/parcels/`：Hadoop相关服务安装目录；
+ `/opt/cloudera/parcel-repo/`：下载的服务软件包数据，数据格式为parcels；
+ `/opt/cloudera/parcel-cache/`：下载的服务软件包缓存数据；
+ `/etc/hadoop/*`：客户端配置文件目录；

