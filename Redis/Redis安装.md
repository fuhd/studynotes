Redis安装
========================================================
Redis安装，推荐的方式是 **通过源码** 来安装！

### 在CentOS7下安装Redis最新版本
Redis的官网为： [http://redis.io/](http://redis.io/)。

#### 下载源代码
```powershell
curl -O http://download.redis.io/releases/redis-3.2.0.tar.gz
```
注意：我这里使用的是Root帐号。

#### 解压，编译源代码并安装
```powershell
tar -xvf redis-3.2.0.tar.gz
cd redis-3.2.0
make && make install
```
执行`make install`。会将make编译生成的可执行文件拷贝到`/usr/local/bin`目录下!!!

另外，在源代码目录的`util`子目录下有一个`install_server.sh`命令，运行它可以 **配置Redis随系统启动**。
