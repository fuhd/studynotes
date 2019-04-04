安装Ambari
================================================================================
要在群集中的单个主机上安装Ambari服务器，请完成以下步骤：
+ 下载Ambari存储库
+ 安装Ambari服务器
+ 设置Ambari服务器

## 1.下载Ambari存储库
按照运行安装主机的操作系统一节中的说明进行操作。

### RHEL/CentOS/Oracle Linux7
在具有Internet访问权限的服务器主机上，使用命令行编辑器执行以下操作。

#### 步骤
+ 以root用户身份登录主机。
+ 将Ambari存储库文件下载到安装主机上的目录中。
    ```shell
    wget -nv http://public-repo-1.hortonworks.com/ambari/centos7/2.x/updates/2.7.3.0/ambari.repo -O /etc/yum.repos.d/ambari.repo
    ```


































dd
