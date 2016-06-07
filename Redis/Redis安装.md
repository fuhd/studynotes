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
执行`make install`会将make编译生成的可执行文件拷贝到`/usr/local/bin`目录下!!!

另外，在源代码目录的`util`子目录下有一个`install_server.sh`命令，运行它可以 **配置Redis随系统启动**。
```powershell
./utils/install_server.sh
```
其间有很多步骤要设置，只要一路默认确定即可！！！！

#### 启动与停止
可以使用这两个命令进行控制：
```powershell
service redis_6379 start
service redis_6379 stop
```

### 在Ubuntu16.04中安装Redis最新版本
#### 下载源代码
```powershell
wget http://download.redis.io/releases/redis-3.2.0.tar.gz
```
#### 解压，编译源代码并安装
```powershell
tar -xvf redis-3.2.0.tar.gz
cd redis-3.2.0
make && sudo make install
sudo ./utils/install_server.sh
```
**注意**：我在自己开发笔记本上安装的，非Root帐号，所以用了`sudo`!

#### 启动与停止
可以使用这两个命令进行控制：
```powershell
service redis_6379 start
service redis_6379 stop
```
