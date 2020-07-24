HAMIS Zeus计算中台安装手册
===================================================================================
## 安装示例
3台服务器搭建最小集群, 服务器如下：

| ip            | hostname   | 配置      | 服务                                                         | 备注        |
| ------------- | ---------- | --------- | ------------------------------------------------------------ | ----------- |
| 172.16.100.30 | bigdata005 | 4Core 32G | namenode resourceManager datanode zookeeper mysql redis JournalNode zeus-maxcomputer | 主节点      |
| 172.16.100.31 | bigdata006 | 4Core 32G | namenode resourceManager datanode zookeeper JournalNode      | standby节点 |
| 172.16.100.32 | bigdata008 | 4Core 32G | datanode  nodeManager zookeeper JournalNode zeus-jobserver spark-thrift | standby节点 |

### 1. 创建用户
步骤如下：
```shell
#配置 admin 账号
adduser admin
passwd admin
# 配置sudo 权限
echo 'admin        ALL=(ALL)       NOPASSWD: ALL' >> /etc/sudoers 
```
### 2. 免登陆设置
```shell
#用admin 账号登录bigdata005
#在主节点上执行 ssh-keygen -t rsa  一直回车，生成无密码的密钥对
ssh-keygen -t rsa 
#将公钥添加到认证文件中： 
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys	
#修改.ssh 权限
chmod 600 ~/.ssh/authorized_keys
#将密钥复制到各个节点
scp ~/.ssh/authorized_keys admin@172.16.100.30:~/.ssh/
scp ~/.ssh/authorized_keys admin@172.16.100.31:~/.ssh/
scp ~/.ssh/authorized_keys admin@172.16.100.32:~/.ssh/
```

### 3.最大文件限制数
在每个节点上操作
1. 查看当前默认的ulimit值,默认值是1024
```shell
ulimit -n
```
2. 将ulimit 加入到`/etc/profile`文件底部
```shell
echo ulimit -n 65535 >>/etc/profile
```
3. 不重启立即生效执行命令 
```shell
source /etc/profile
```

### 4.创建相关目录
```shell
#创建hadoop 目录
sudo mkdir -p /bigdata
#创建安装包路径
sudo mkdir -p /bigdata/install/package/
#修改所属用户
sudo chown -R admin:admin /bigdata
```

## 安装步骤

### 1.JDK安装
```shell
#将jdk 安装到 /usr/local目录
sudo tar zxf /bigdata/install/package/jdk-8u231-linux-x64.tar.gz -C /usr/local/
#在 /etc/profile 配置如下环境变量
export JAVA_HOME=/usr/local/jdk1.8.0_231
export PATH=$PATH:$JAVA_HOME/bin
#生效配置
source  /etc/profile
```

### 2.python安装
升级Python到 3.6 版本,  `/usr/bin/python3`可正常执行

### 3.zookeeper安装 
配置集群服务
```shell
#在服务器 bigdata005执行如下操作：
sudo tar zxf /bigdata/install/package/zookeeper-3.4.14.tar.gz -C /usr/local/
sudo vi /etc/profile
```
添加:
```shell
export ZOOKEEPER_HOME=/usr/local/package/zookeeper-3.4.14
export PATH=$PATH:$ZOOKEEPER_HOME/bin
```
```shell
#环境变量生效
source /etc/profile
# 先将zoo_sample.cfg重命名为zoo.cfg
mv $ZOOKEEPER_HOME/conf/zoo_sample.cfg $ZOOKEEPER_HOME/conf/zoo.cfg
# 修改参数
sudo vi $ZOOKEEPER_HOME/conf/zoo.cfg 
```
添加:
```ini
dataDir=/bigdata/zookeeper/data
# 2181是默认端口，如果端口占用可以改成别的，比如21810等
clientPort=2181 
server.1=bigdata005:2888:3888
server.2=bigdata006:2888:3888
server.3=bigdata008:2888:3888
```
```shell
#创建data目录
mkdir -p /bigdata/zookeeper/data    #每台机器都执行
#设置服务ID作为集群中节点标识
vi /bigdata/zookeeper/data/myid
```
```
1
```
备注：myid里是数值要和zoo.cfg里的服务器对应上。每台服务器的数值不一样。将`/usr/local/zookeeper-3.4.14`
复制到其他服务器对应的目录上，并修改`/bigdata/zookeeper/data/myid`。

启动集群服务
```shell
#在部署zookeeper的节点执行命令
cd  /usr/local/zookeeper-3.4.14/bin
每台服务器都执行
./zkServer.sh start #
#查看状态
./zkServer.sh status 
```

### 4.Hadoop安装

#### 4.1.环境变量配置
```shell
#解压安装
sudo tar -zxf /bigdata/install/package/hadoop-2.7.7.tar.gz -C /usr/local/
#环境变量配置
sudo vi /etc/profile
```
添加:
```ini
# HADOOP_HOME
export HADOOP_HOME=/usr/local/hadoop-2.7.7
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```
```shell
#生效环境变量
source /etc/profile 
```

#### 4.2.配置三个环境文件
将/usr/local/hadoop-2.7.7/etc/hadoop/下的三个文件[hadoop-env.sh](http://hadoop-env.sh/)，[mapred-env.sh](http://mapred-env.sh/)，[yarn-env.sh](http://yarn-env.sh/)中添加 JAVA_HOME 修改为自己下载的jdk地址

```
export JAVA_HOME=/usr/local/jdk1.8.0_231
```



#### 2.配置core-site.xml文件

```
<configuration>
<!-- 指定hdfs的nameservice为ns -->
<property>
        <name>fs.defaultFS</name>
        <value>hdfs://ns</value>
</property>
<!-- 指定hadoop临时目录 -->
<property>
        <name>hadoop.tmp.dir</name>
        <value>/bigdata/tmp</value>
</property>
<!-- 指定zk -->
<property>
        <name>ha.zookeeper.quorum</name>
        <value>bigdata005:2181,bigdata006:2181,bigdata008:2181</value>
</property>
<!-- Namenode向JournalNode发起的ipc连接请求的重试最大次数 -->
<property>
        <name>ipc.client.connect.max.retries</name>
        <value>100</value>
        <description>Indicates the number of retries a client will make to establish a server connection.
        </description>
</property>
<!-- Namenode向JournalNode发起的ipc连接请求的重试间隔时间 -->
<property>
        <name>ipc.client.connect.retry.interval</name>
        <value>10000</value>
        <description>Indicates the number of milliseconds a client will wait for before retrying to establish a server connection.
        </description>
</property>
<!-- 开启回收功能，并设置垃圾删除间隔(分钟) -->
<property>
        <name>fs.trash.interval</name>
        <value>360</value>
        <description>Trash deletion interval in minutes.If zero, the trash feature is disabled.</description>
</property>
<!-- 设置垃圾检查点间隔(分钟)，不设置的话默认和fs.trash.interval一样 -->
<property>
        <name>fs.trash.checkpoint.interval</name>
        <value>60</value>
        <description>Trash checkpoint interval in minutes.If zero, the deletion interval is used.</description>
</property>
<!-- 下面的两个参数是配置oozie时使用的 -->
<property>
        <name>hadoop.proxyuser.deplab.groups</name>
        <value>*</value>
</property>
<property>
        <name>hadoop.proxyuser.deplab.hosts</name>
        <value>*</value>
</property>
</configuration>
```



#### 3.配置hdfs-site.xml文件

```
<configuration>
<!-- hdfs的存放目录，集群中挂载硬盘的目录最好一致 -->
<property>
        <name>dfs.datanode.data.dir</name>
        <value>/bigdata/hdfs/data</value>
        <final>true</final>
</property>
<!--指定hdfs的nameservice为ns，需要和core-site.xml中的保持一致 -->
<property>
        <name>dfs.nameservices</name>
        <value>ns</value>
</property>
<!-- ns下面有两个NameNode，分别是nn1，nn2 -->
<property>
        <name>dfs.ha.namenodes.ns</name>
        <value>nn1,nn2</value>
</property>
<!-- nn1的RPC通信地址，8020端口换成9000端口也行 -->
<property>
        <name>dfs.namenode.rpc-address.ns.nn1</name>
        <value>bigdata005:8020</value>
</property>
<!-- nn1的http通信地址 -->
<property>
        <name>dfs.namenode.http-address.ns.nn1</name>
        <value>bigdata005:50070</value>
</property>
<!-- nn2的RPC通信地址，8020端口换成9000端口也行 -->
<property>
        <name>dfs.namenode.rpc-address.ns.nn2</name>
        <value>bigdata006:8020</value>
</property>
<!-- nn2的http通信地址 -->
<property>
        <name>dfs.namenode.http-address.ns.nn2</name>
        <value>bigdata006:50070</value>
</property>
<!-- 指定NameNode的元数据在JournalNode上的存放位置 -->
<property>
        <name>dfs.namenode.shared.edits.dir</name>
        <value>qjournal://bigdata005:8485;bigdata006:8485;bigdata008:8485/ns</value>
</property>
<!-- 指定JournalNode在本地磁盘存放数据的位置 -->
<property>
        <name>dfs.journalnode.edits.dir</name>
        <value>/bigdata/jn/edits</value>
</property>
<!-- 开启NameNode故障时自动切换 -->
<property>
        <name>dfs.ha.automatic-failover.enabled</name>
        <value>true</value>
</property>
<!-- 配置失败自动切换实现方式 -->
<property>
        <name>dfs.client.failover.proxy.provider.ns</name>
        <value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
</property>
<!-- 配置隔离机制，如果ssh是默认22端口，value直接写sshfence即可 -->
<property>
        <name>dfs.ha.fencing.methods</name>
        <value>
                sshfence
                shell(/bin/true)
        </value>
</property>
<!-- 使用隔离机制时需要ssh免登陆 -->
<property>
        <name>dfs.ha.fencing.ssh.private-key-files</name>
        <value>/home/admin/.ssh/id_rsa</value>
</property>
<!-- 配置sshfence隔离机制超时时间 -->
<property>
        <name>dfs.ha.fencing.ssh.connect-timeout</name>
        <value>30000</value>
</property>
<!-- hdfs 元数据所在文件夹 -->
<property>
<name>dfs.namenode.name.dir</name>
<value>/bigdata/hdfs/name</value>
<final>true</final>
</property>
<!-- hdfs 文件备份数量，默认是3个 -->
<property>
        <name>dfs.replication</name>
        <value>3</value>
</property>
<!-- 开启webhdfs服务 -->
<property>
        <name>dfs.webhdfs.enabled</name>
        <value>true</value>
</property>
<!-- 取消向hdfs上写数据的用户权限设置 -->
<property>
        <name>dfs.permissions.enabled</name>
        <value>false</value>
</property>
<!-- 此参数用于动态添加删除datanode节点 -->
<property>
        <name>dfs.hosts.exclude</name>
        <value>/usr/local/hadoop-2.7.7/etc/hadoop/excludes</value>
</property>

</configuration>
```



#### 4.配置mapred-site.xml文件

```
<configuration>
<property>
 <name>mapreduce.framework.name</name>
 <value>yarn</value>
 </property>

<property>
        <name>mapreduce.jobhistory.address</name>
        <value>bigdata005:10020</value>
</property>
<property>
        <name>mapreduce.jobhistory.webapp.address</name>
        <value>bigdata005:19888</value>
</property>
</configuration>
```



#### 5.配置yarn-site.xml文件

```
<configuration>
<property>
        <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
        <value>org.apache.mapred.ShuffleHandler</value>
</property>
<property>
        <name>yarn.nodemanager.aux-services</name>
        <value>spark_shuffle,mapreduce_shuffle</value>
</property>

<property>
        <name>yarn.nodemanager.aux-services.spark_shuffle.class</name>
        <value>org.apache.spark.network.yarn.YarnShuffleService</value>
</property>

<property>
  <name>yarn.resourcemanager.scheduler.class</name>
  <value>org.apache.hadoop.yarn.server.resourcemanager.scheduler.fair.FairScheduler</value>
</property>

<property>
  <name>yarn.scheduler.fair.user-as-default-queue</name>
  <value>true</value>
</property>

<!--启用resourcemanager ha-->
<property>
        <name>yarn.resourcemanager.ha.enabled</name>
        <value>true</value>
</property>
<property>
        <name>yarn.resourcemanager.cluster-id</name>
        <value>rmcluster</value>
</property>
<property>
        <name>yarn.resourcemanager.ha.rm-ids</name>
        <value>rm005,rm008</value>
</property>
<property>
        <name>yarn.resourcemanager.hostname.rm005</name>
        <value>bigdata005</value>
</property>
<property>
        <name>yarn.resourcemanager.hostname.rm008</name>
        <value>bigdata008</value>
</property>


<property>
 <name>yarn.resourcemanager.webapp.address.rm005</name>
 <value>bigdata005:8099</value>
</property>

<property>
 <name>yarn.resourcemanager.webapp.address.rm008</name>
 <value>bigdata008:8099</value>
</property>
<!--指定zookeeper集群的地址-->
<property>
        <name>yarn.resourcemanager.zk-address</name>
        <value>bigdata005:2181,bigdata006:2181,bigdata008:2181</value>
</property>
<!--启用自动恢复-->
<property>
        <name>yarn.resourcemanager.recovery.enabled</name>
        <value>true</value>
</property>
<!--指定resourcemanager的状态信息存储在zookeeper集群-->
<property>
        <name>yarn.resourcemanager.store.class</name>
        <value>org.apache.hadoop.yarn.server.resourcemanager.recovery.ZKRMStateStore</value>
</property>
<!--启用日志聚合功能-->
<property>
        <name>yarn.log-aggregation-enable</name>
        <value>true</value>
</property>
<property>
        <name>yarn.log.server.url</name>
        <value>http://bigdata005:19888/jobhistory/job/</value>
</property>
<!--日志保存时间-->
<property>
        <name>yarn.log-aggregation.retain-seconds</name>
        <value>604800</value>
</property>
<!-- 该nodemanager节点上YARN可使用的物理内存总量，默认是8192（MB） -->
<property>
        <name>yarn.nodemanager.resource.memory-mb</name>
        <value>20480</value>
</property>
<!-- 该nodemanager节点上YARN可使用的虚拟CPU个数，默认是8 -->
<property>
        <name>yarn.nodemanager.resource.cpu-vcores</name>
        <value>7</value>
</property>
<!-- 单个任务可申请的最少物理内存量，默认是1024（MB） -->
<property>
        <name>yarn.scheduler.minimum-allocation-mb</name>
        <value>1024</value>
</property>
<!-- 单个任务可申请的物理内存量上限 -->
<property>
        <name>yarn.scheduler.maximum-allocation-mb</name>
        <value>10240</value>
</property>
<!-- 取消虚拟内存检查 -->
<property>
        <name>yarn.nodemanager.vmem-check-enabled</name>
        <value>false</value>
</property>

<property>
        <name>yarn.resourcemanager.address.rm005</name>
        <value>bigdata005:8032</value>
</property>
<property>
        <name>yarn.resourcemanager.scheduler.address.rm005</name>
        <value>bigdata005:8030</value>
</property>
<property>
        <name>yarn.resourcemanager.resource-tracker.address.rm005</name>
        <value>bigdata005:8031</value>
</property>

<property>
        <name>yarn.resourcemanager.address.rm008</name>
        <value>bigdata008:8032</value>
</property>
<property>
        <name>yarn.resourcemanager.scheduler.address.rm008</name>
        <value>bigdata008:8030</value>
</property>
<property>
        <name>yarn.resourcemanager.resource-tracker.address.rm008</name>
        <value>bigdata008:8031</value>
</property>

</configuration>
```



#### 6.配置slaves文件

编辑/usr/local/hadoop-2.7.7/etc/hadoop/slaves，添加以下内容

```
bigdata005
bigdata006
bigdata008
```



#### 7.分发hadoop-2.7.7到其他机器上

```
scp -r /usr/local/hadoop-2.7.7 root@bigdata005://usr/local/
scp -r /usr/local/hadoop-2.7.7 root@bigdata008://usr/local/
cd /usr/local/
sudo chown -R  admin:admin hadoop-2.7.7
```



#### 8.hadoop启动

a.重启 **zk**

```
zkServer.sh stop
zkServer.sh start
```



b.启动 **JournalNode** (3台机器)
在 **dfs.namenode.shared.edits.dir** 参数指定的机器上启动 **JournalNode** ，特别强调 **JournalNode** 必须设置 **奇数** 台

```
cd /usr/local/hadoop-2.7.7/sbin

./hadoop-daemon.sh start journalnode
```



**journalnode**启动成功，用 **jps** 可以看到名为 **JournalNode**的守护进程

c.格式化HDFS并启动namenode(bigdata005上)

\# 格式化HDFS命令   

```
hdfs namenode -format      

# 启动 namenode 

hadoop-daemon.sh start namenode 
```

\#  在bigdata006同步nn1 元数据,执行命令

```
hdfs namenode -bootstrapStandby
```

\# 启动 namenode

```
hadoop-daemon.sh start namenode
```

此时目前两个namenode都是standby(可以查看50070界面)，可以在其中一台机器上输入 如下命令，强制将nn2变成active

```
hdfs haadmin -transitionToActive --forcemanual nn2
```



e.初始化zkfc服务(注意先后顺序哦)

```
# 关闭hdfs和重启zk

cd  /usr/local/hadoop-2.7.7/sbin/

./stop-dfs.sh #(主节点)

zkServer.sh stop  #(全部节点)

zkServer.sh start #(全部节点)

# 初始化zkfc

hdfs zkfc -formatZK #(主节点)
```



f.启动hdfs和yarn

```
# 启动hdfs

cd  /usr/local/hadoop-2.7.7/sbin

start-dfs.sh

\# 启动yarn

start-yarn.sh #(主节点)

\#deptest3上启动另一个ResourceManager

yarn-daemon.sh start resourcemanager #(rm008)  在bigdata008
```



g.查看状态并测试

```
hdfs haadmin -getServiceState nn1 #查看nn1状态命令

hdfs haadmin -getServiceState nn2 #查看nn2状态命令

yarn rmadmin -getServiceState rm005 #查看rm1的状态命令

yarn rmadmin -getServiceState rm008 #查看rm2的状态命令
```



### mysql安装

1、去官网查看最新安装包

```
https://dev.mysql.com/downloads/repo/yum/
```



2、下载MySQL源安装包

```
#在bigdata005上配置
cd /bigdata/install/package/
wget http://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
```

安装MySql源

```
yum -y install mysql57-community-release-el7-11.noarch.rpm
```



查看一下安装效果

```
yum repolist enabled | grep mysql.*
```



3、安装MySQL服务器

```
yum install mysql-community-server

#中间会弹出是与否的选择，选择y即可
```

4、启动MySQL服务

```
systemctl start  mysqld.service
```

运行一下命令查看一下运行状态 

```
systemctl status mysqld.service
```



5、初始化数据库密码

查看一下初始密码

```
grep "password" /var/log/mysqld.log
```

登录

```
mysql -uroot -p
```

修改密码

```
ALTER USER 'root'@'localhost' IDENTIFIED BY '****************';
```

mysql默认安装了密码安全检查插件（validate_password），默认密码检查策略要求密码必须包含：大小写字母、数字和特殊符号，并且长度不能少于8位。否则会提示ERROR 1819 (HY000): Your password does not satisfy the current policy requirements错误



6、数据库授权

数据库没有授权，只支持localhost本地访问

```
mysql>GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
```

//远程连接数据库的时候需要输入用户名和密码

用户名：root

密码:123456

指点ip:%代表所有Ip,此处也可以输入Ip来指定Ip

输入后使修改生效还需要下面的语句

```
mysql>FLUSH PRIVILEGES;
```

也可以通过修改表来实现远程：

    mysql -u root -p
    
    mysql> use mysql; 
    mysql> update user set host = '%' where user = 'root'; 
    mysql> select host, user from user;

7、设置自动启动

```
systemctl enable mysqld

systemctl daemon-reload
```




### hive 安装

1、环境变量配置

```
sudo vi /etc/profile

#hive
export HIVE_HOME=/usr/local/hadoop-2.7.7/hive/apache-hive-1.2.1-bin
export PATH=$PATH:$HIVE_HOME/bin

#环境变量生效
source /etc/profile
```



2、在mysql创建hive用户

```
#在mysql上执行
GRANT ALL PRIVILEGES ON *.* TO 'hive'@'%' IDENTIFIED BY 'hive'  WITH GRANT OPTION;
```



3、复制mysql的驱动程序到hive/lib下面



4、修改hive-site.xml配置

```

cd  /usr/local/hadoop-2.7.7/hive/apache-hive-1.2.1-bin/conf
sudo vi hive-site.xml

<configuration>
  <property>
    <name>javax.jdo.option.ConnectionURL</name>
    <!-- 如果hive库不存在，则创建 -->
    <value>jdbc:mysql://bigdata005:3306/hive?createDatabaseIfNotExist=true</value>
    <description>JDBC connect string for a JDBC metastore</description>
  </property>

  <property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>com.mysql.jdbc.Driver</value>
    <description>Driver class name for a JDBC metastore</description>
  </property>

  <property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>hive</value>
    <description>username to use against metastore database</description>
  </property>

  <property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>hive</value>
    <description>password to use against metastore database</description>
  </property>

  <!-- 显示当前使用的数据库 -->
  <property>
    <name>hive.cli.print.current.db</name>
    <value>true</value>
    <description>Whether to include the current database in the Hive prompt.</description>
  </property>

<property>
  <name>hive.metastore.uris</name>
  <value>thrift://bigdata005:9083,thrift://bigdata006:9083</value>
  <description>IP address (or fully-qualified domain name) and port of the metastore host</description>
</property>

<property>
 <name>hive.metastore.warehouse.dir</name>
 <value>/user/hive/warehouse</value>
</property>

<property>
 <name>hive.exec.scratchdir</name>
 <value>/bigdata/hive/tmp</value>
</property>

<property>
 <name>hive.querylog.location</name>
 <value>/var/log/hive</value>
</property>

</configuration>
```



5、启动服务

```
cd /usr/local/hadoop-2.7.7/hive/apache-hive-1.2.1-bin/

#启动元数据服务
bin/hive --service metastore &    

#启动hiveserver2服务
bin/hive --service hiveserver2 &
```

6、验证服务

```
cd /usr/local/hadoop-2.7.7/hive/apache-hive-1.2.1-bin/bin
#执行命令
hive
```



### impala安装

1、准备impala的所有依赖包

```
sudo tar zxf cdh5.16.1-centos7.tar.gz -C /usr/local/
```



2、制作本地yum源
bigdata005机器上执行以下命令

```
yum  -y install httpd
service httpd start
cd /etc/yum.repos.d

vim localimp.repo 
[localimp]
name=localimp
baseurl=http://bigdata005/cdh5.16.1/
gpgcheck=0
enabled=1
```

3、创建apache httpd的读取链接,(快捷文件)

```
ln -s /data02/cdh/5.16.1 /var/www/html/cdh5.16.1
```

页面访问本地yum源，出现这个界面表示本地yum源制作成功

```
http://bigdata005/cdh5.16.1/
```



4、将制作好的localimp配置文件发放到所有需要安装impala的节点上去

```
cd /etc/yum.repos.d/
scp localimp.repo  bigdata006:$PWD
scp localimp.repo  bigdata008:$PWD
```



5、主节点bigdata005执行以下命令进行安装

```
yum  install  impala -y
yum install impala-server -y
yum install impala-state-store  -y
yum install impala-catalog  -y
yum  install  impala-shell -y
```

6、从节点bigdata006与bigdata008安装以下服务

```
yum install impala-server -y
```



7、创建hadoop与hive的配置文件的连接
所有节点执行以下命令创建链接到impala配置目录下来

```
sudo ln -s /usr/local/hadoop-2.7.7/etc/hadoop/core-site.xml    /etc/impala/conf/core-site.xml
sudo ln -s  /usr/local/hadoop-2.7.7/etc/hadoop/hdfs-site.xml   /etc/impala/conf/hdfs-site.xml
sudo ln -s /usr/local/hadoop-2.7.7/hive/apache-hive-1.2.1-bin/conf/hive-site.xml   /etc/impala/conf/hive-site.xml
```



8、修改impala的配置文件
所有节点修改impala默认配置

```
vim /etc/default/impala
IMPALA_CATALOG_SERVICE_HOST=bigdata005
IMPALA_STATE_STORE_HOST=bigdata005
```

9、所有节点创建mysql的驱动包的软连接

```
sudo ln -s /usr/local/hadoop-2.7.7/hive/apache-hive-1.2.1-bin/lib/mysql-connector-java.jar    /usr/share/java/mysql-connector-java.jar
```

10、所有节点修改bigtop的java路径
        修改bigtop的java_home路径

```
sudo vim /etc/default/bigtop-utils
export JAVA_HOME=/usr/local/jdk1.8.0_231
```



11、启动impala服务
        启动impala服务，先启动metastore
        主节点bigdata005启动以下三个服务进程

```
service impala-state-store start
service impala-catalog start
service impala-server start
```


​        从节点启动node01与bigdata008启动impala-server

```
service  impala-server  start
```

​         查看impala进程是否存在

```
ps -ef | grep impala
```

注意：启动之后所有关于impala的日志默认都在/var/log/impala 这个路径下，bigdata005机器上面应该有三个进程，bigdata006与bigdata008机器上面只有一个进程，如果进程个数不对，去对应目录下查看报错日志





### redis 安装

1、安装gcc依赖

由于 redis 是用 C 语言开发，安装之前必先确认是否安装 gcc 环境（gcc -v），如果没有安装，执行以下命令进行安装

```
#在bigdata005上安装
yum install -y gcc
```

 

2、下载并解压安装包

```
 wget http://download.redis.io/releases/redis-5.0.3.tar.gz

 tar -zxvf redis-5.0.3.tar.gz
```

 

3、cd切换到redis解压目录下，执行编译

```
cd redis-5.0.3

make
```

 

4、安装并指定安装目录

```
make install PREFIX=/usr/local/redis
```

 

5、启动服务

从 redis 的源码目录中复制 redis.conf 到 redis 的安装目录

```
 cp /usr/local/redis-5.0.3/redis.conf /usr/local/redis/bin/

#修改配置参数
vi redis.conf
#bind 127.0.0.1     #注销掉这行，开启远程连接
daemonize yes   
logfile /var/log/redis/redis.log        #记得手工创建目录
dir /var/lib/redis    #记得手工创建目录
requirepass 123  #指定密码123

#启动服务
./redis-server redis.conf
```



6、创建 redis 命令软链接

```
 ln -s /usr/local/redis/bin/redis-cli /usr/bin/redis

```

7、关闭命令

```
./redis-cli -h 127.0.0.1 -pZjw11763bigdata! 6379 shutdown
```

 

### maxcompute安装

1、根据hadoop的core-site.xml和hdfs-site.xml，修改datacenter/cec/prod/core-site.xml配置

​	![image-20200717115955105](安装手册.assets/image-20200717115955105.png)

![image-20200717120117656](安装手册.assets/image-20200717120117656.png)



2、数据库初始化

```
在mysql创建数据库maxcompute2，设置为UTF-8编码。
在maxcompute2库下执行maxcompute_init.sql，sql文件在data-ops-zeus\hamis-zeus-1.2.0-bin\config。
```

3、第三方库安装

```
cd data-ops-zeus/hamis-zeus-1.2.0-src/zeus-client/maxcompute  #到自己本地路径上

mvn install:install-file -DgroupId=com.seeyon -DartifactId=ctp -Dversion=2.0.0 -Dpackaging=jar -Dfile=jar/ctp-2.0.0.jar    -Dmaven.test.skip

mvn install:install-file -DgroupId=cn.fraudmetrix.cache -DartifactId=jcache-provider -Dversion=1.0.0 -Dpackaging=jar -Dfile=jar/jcache-provider-1.0.0.jar      -Dmaven.test.skip

mvn install:install-file -DgroupId=com.esen.jdbc -DartifactId=gbase -Dversion=8.3.81.53 -Dpackaging=jar -Dfile=jar/gbase-8.3.81.53.jar     -Dmaven.test.skip
```

4、安装 bee jar 到仓库(数据库通用访问)

```
cd  data-ops-zeus/hamis-zeus-1.2.0-src/zeus-client/bee
mvn clean install  -Dmaven.test.skip
```

5、安装 maxcompute-parser jar到仓库(计算引擎 sql 解析层)

```
cd data-ops-zeus/hamis-zeus-1.2.0-src/zeus-client/maxcompute-parser
mvn clean install  -Dmaven.test.skip
```

6、编译打包zeus-maxcompute

```
cd data-ops-zeus/hamis-zeus-1.2.0-src/zeus-client/maxcompute
mvn clean package -Dmaven.test.skip
```

7、服务部署

```
#将target下的maxcompute-web-1.0.0.jar放到/usr/local/appserver
scp E:\alpha\gitlab\maxcompute-v1.0.0\maxcompute\web\target\maxcompute-web-1.0.0.jar admin@10.0.21.1:/usr/local/appserver/
```

8、服务启动

```
#将startup_maxcompute.sh放到/usr/local/appserver

sh /usr/local/appserver/startup_maxcompute.sh
```

9、修改集群配置信息

```
点击http://bigdata005:8181/cluster/index，进入运维中心-》集群管理，点击编辑按钮，将hadoop下的配置文件信息copy到对应位置。分别需要修改的文件包含

core-site.xml, hdfs-site.xml, mapred-site.xml, hive-site.xml, yarn-site.xml 。
```

10、创建t_table_partition表

```
在mysql的hive库下创建t_table_partition表，建表语句参考data-ops-zeus\hamis-zeus-1.2.0-bin\config\t_table_partition.sql。
```

11、config参数配置

打开 http://bigdata005:8181/config 页面，appname选择maxcompute，profile选择生产环境

```
#配置上链信息
dc.oplog.sendtochain=true
dc.oplog.sendtochain.url=http://10.0.22.12:9021/dmp-blockchain-api/oxchain/save

##配置hamis门户
dc.platform.hamis.url=https://platform.ssc.hamis.alphaif.com/
```

12、推荐系统回调接口配置

```
#进入maxcompute2库下的dc_user_info表，编辑callback_url_json值为：

https://venus.hamis.alphaif.com/venus/configcenter/api/v2/hamis/venus/configCenter/callback
```

13、本地启动服务

```
配置VM options值：-Dspring.profiles.active=prod，参考如下示例图。
```

![image-20200717115405362](安装手册.assets/image-20200717115405362.png)



### spark 安装

1、spark源码打包

```
#cd /e/alpha/gitlab/spark2.4.0   在自己本地项目路径
dev/make-distribution.sh --tgz  -Pyarn -Phive-thriftserver -Phive-1.2.1  -Dhadoop.version=2.7.7 -Dyarn.version=2.7.7 -DskipTests
```

2、安装spark-core jar到仓库

```
mvn install:install-file -DgroupId=org.apache.spark -DartifactId=spark-core_2.11  -Dversion=2.4.0  -Dpackaging=jar  -Dfile=/e/alpha/gitlab/spark2.4.0/core/target/spark-core_2.11-2.4.0.jar -Dmaven.test.skip  -Dcheckstyle.skip=true  -DpomFile=/e/alpha/gitlab/spark2.4.0/core/pom.xml  
```

3、服务部署

```
#将本地打包好的spark-2.4.0-bin-2.7.7.tgz放到bigdata008服务器，进行解压安装
sudo tar -zxvf spark-2.4.0-bin-2.7.7.tgz -C /usr/local/
```

4、创建相关文件夹

```
cd   /home/admin/
mkdir eventlogs
mkdir jobinstancelogs
mkdir -p jobserver-config/spark-2.4
mkdir  jobserver-jars
mkdir jars
```

5、相关jar上传到hdfs

```
将\xxx\data-ops-zeus\hamis-zeus-1.2.0-bin\lib\aspectjweaver-1.8.10.jar 放到  /home/admin/jars/

cd /usr/local/hadoop-2.7.7

bin/hadoop dfsadmin -safemode leave    #离开安全模式

#将spark相关jar放入hdfs
bin/hdfs dfs -put /usr/local/spark-2.4.0-bin-2.7.7/jars/*   /user/maxcompute/yarn_jars/spark_2.4.0/
bin/hdfs dfs -put /home/admin/jars/aspectjweaver-1.8.10.jar /user/maxcompute/yarn_jars/

```

6、软链接设置

```
#软链接hadoop和hive的配置文件
sudo  ln -s  /usr/local/hadoop-2.7.7/etc/hadoop/hdfs-site.xml  /usr/local/spark-2.4.0-bin-2.7.7/conf/hdfs-site.xml 
sudo  ln -s  /usr/local/hadoop-2.7.7/hive/apache-hive-1.2.1-bin/conf/hive-site.xml  /usr/local/spark-2.4.0-bin-2.7.7/conf/hive-site.xml 
```

7、修改环境变量

```
vi   /usr/local/spark-2.4.0-bin-2.7.7/conf/spark-env.sh

export SPARK_LOCAL_IP=bigdata008
export SPARK_HOME=/usr/local/spark-2.4.0-bin-2.7.7
```

8、启动thriftserver

```
/usr/local/spark-2.4.0-bin-2.7.7/sbin/start-thriftserver.sh
```



### jobserver安装

1、编译源码

```
cd /e/alpha/gitlab/jobserver
mvn clean install -Dmaven.test.skip
```

2、服务部署

```
#在bigdata008上操作
#安装tomcat到/usr/local/appserver/dc-tomcat
将/xxx/jobserver/jobserver-control/target/ROOT.war 放到/usr/local/appserver/dc-tomcat/webapps
```

3、jar上传到hdfs

```
将本地的jobserver-yarn-2.4.0.jar放到/home/admin/
bin/hdfs dfs -put -f /home/admin/jobserver-yarn-2.4.0.jar /user/maxcompute/yarn_jars/spark_2.4.0/
```





### flink安装

```
#解压后即安装

sudo tar -zxvf  /bigdata/install/package/flink-1.8.1.tar.gz  -C /usr/local/
```



### alphax安装

1、install jar包

```
#install相关jar包，jar位于alphax的jar文件夹下

mvn install:install-file -DgroupId=com.ibm.db2 -DartifactId=db2jcc -Dversion=3.72.44 -Dpackaging=jar -Dfile=db2jcc-3.72.44.jar

mvn install:install-file -DgroupId=com.github.noraui -DartifactId=ojdbc8 -Dversion=12.2.0.1 -Dpackaging=jar -Dfile=/xxx/ojdbc8-12.2.0.1.jar

mvn install:install-file -DgroupId=com.esen.jdbc -DartifactId=gbase -Dversion=8.3.81.53 -Dpackaging=jar -Dfile=/xxx/gbase-8.3.81.53.jar

mvn install:install-file -DgroupId=com.dm -DartifactId=Dm7JdbcDriver18 -Dversion=7.6.0.197 -Dpackaging=jar -Dfile=/xxx/Dm7JdbcDriver18.jar
```

2、编译打包

```
#进入本地alphax目录，执行以下命令编译打包
mvn clean package -Dmaven.test.skip
```

3、服务部署

```
打包后将bin,lib,plugins放到bigdata008上的/usr/local/alphax/
```

4、参数配置

```
#在config页面添加alphax配置参数
#打开 http://bigdata005:8181/config 页面，appname选择maxcompute，profile选择生产环境，添加以下内容
#以下为示例数据，请根据自身配置改动

dc.alphax.apiUrl=http://bigdata008:9898/
dc.alphax.pluginRoot=/usr/local/alphaX/plugins
dc.alphax.flinkconf=/usr/local/flink-1.8.1/conf
dc.alphax.yarnconf=/usr/local/hadoop-2.7.7/etc/hadoop
dc.alphax.flinkLibJar=/usr/local/flink-1.8.1/lib
dc.alphax.mode=yarn
dc.alphax.confProp={"flink.checkpoint.interval":50000}
```

5、#启动服务

```
su admin
sh /usr/local/alphaX/bin/alphax_submit.sh
```



### flink-sql安装

1、编译打包

```
#进入本地flink-sql目录，执行以下命令编译打包
mvn clean package -Dmaven.test.skip
```

2、服务部署

```
#打包后将bin,lib,plugins放到bigdata005上的/usr/local/flink-sql/
```

3、config参数配置

```
#在config页面添加alphax配置参数
#打开 http://bigdata005:8181/config 页面，appname选择maxcompute，profile选择生产环境，添加以下内容
#以下为示例数据，请根据自身配置改动

dc.flinkStream.apiUrl=http://bigdata005:8484/
dc.flink.sql.path=/tmp/
dc.flink.localSqlPluginPath=/usr/local/flink-sql/plugins
dc.flink.flinkconf=/usr/local/flink-1.8.1/conf
dc.flink.yarnconf=/usr/local/hadoop-2.7.7/etc/hadoop
dc.flink.remoteSqlPluginPath=/usr/local/flink-sql/plugins
dc.flink.flinkJarPath=/usr/local/flink-1.8.1/lib
dc.flink.mode=yarn
dc.flink.pluginLoadMode=shipfile
dc.flink.overview.url=http://bigdata005
```

4、启动服务

```
su admin
sh /usr/local/flink-sql/bin/flink_sql_submit.sh
```



### datasource安装

1、编译打包

```
#进入本地dataSource目录，执行以下命令编译打包
mvn clean package -Dmaven.test.skip
```

2、服务部署

```
#将target下打包后的dataSource.jar放到bigdata008上的/usr/local/datasource/下
#将data-ops-zeus\hamis-zeus-1.2.0-bin\bin下的start_datasource.sh放到bigdata008上的/usr/local/datasource/下
```

3、数据库初始化

```
#创建数据库datasource，设置为UTF-8编码。
#在datasource库下执行datasource_init.sql，sql文件在data-ops-zeus\hamis-zeus-1.2.0-bin\config。
```

4、启动服务

```
sh /usr/local/datasource/start_datasource.sh
```

5、mount挂载服务

```
#mount挂载，读取AI中台,推荐中台提交的文件
sudo mkdir -p /pallas_data/data
sudo mkdir -p /venus_data/data
mount -t nfs 10.0.23.10:/data /pallas_data/data
mount -t nfs 10.0.23.10:/data /venus_data/data
```

6、开机自动挂载

```
#设置开机自动挂载，在客户端/etc/fstab里添加
10.0.23.10:/data /pallas_data/data                    nfs    defaults,_rnetdev        1 1
10.0.23.10:/data /venus_data/data                    nfs    defaults,_rnetdev        1 1
```



### 

