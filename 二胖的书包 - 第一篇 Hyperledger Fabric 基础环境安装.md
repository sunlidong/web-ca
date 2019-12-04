
1. 安装基础环境: 更新 yum, Git安装
2. 安装 Golang 语言环境
3. 安装 Docker 环境，Docker-Compose 环境
4. 安装 Hyperldger Fabric 环境
5. 启动测试



### 1. **准备基础环境: Git安装、更新 yum**
1. 更新 yum  。操作如下：
```shell
yum update -y
```
2. 安装 Git 。操作如下：
```shell
yum install git
```

### 2. **安装 Golang 语言环境**

1. 下载 Golang 源码包。 版本为1.12.7。 操作如下：

```shell
wget https://studygolang.com/dl/golang/go1.12.7.linux-amd64.tar.gz
```

2. 解压源码包至用户级的程序目录 /usr/local 。操作如下：

```shell
sudo tar -C /usr/local -xzf go1.12.7.linux-amd64.tar.gz
```

3. 配置 Golang 环境变量。打开配置文件 etc/profile 。操作如下：

```shell
//打开配置文件
> vi /etc/profile
//配置
export PATH=$PATH:/usr/local/go/bin
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$PATH:$HOME/go/bin
// 设置生效
> source /etc/profile
// 查看go版本
go version
```
查看显示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190729112543697.png)
### 3. **安装 Docker 环境，Docker-Compose 环境**
1. 安装 Docker 。操作如下：

```shell
yum -y install docker
```
2. 启动 Docker 。操作如下：
```shell
systemctl start docker
```
3. 验证 Docker 是否安装成功。 操作如下：
```shell
docker version
```
安装成功应显示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190729112914738.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Jvc3MyOTY3,size_16,color_FFFFFF,t_70)

4. 添加用户组 Docker。 操作如下：
```
> // 如果还没有docker group就添加一个：
> sudo groupadd docker
//安装好docker之后， 可能存在只有root用户才能调用的问题， 需要将当前用户加入docker组中：
> sudo gpasswd -a ${USER} docker
> sudo systemctl restart docker
//重启docker
> systemctl daemon-reload
> systemctl restart docker.service
```

5.  安装 Docker-Compose 环境。 操作如下：
```shell
 curl -L https://github.com/docker/compose/releases/download/1.8.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```
6. 添加 运行权限。操作如下：
```
chmod +x /usr/local/bin/docker-compose
```
7. 验证 Docker-Compose 是否安装成功。操作如下：
```
docker-compose version
```
安装成功会显示如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190729112946569.png)


### 4. **安装 Hyperldger Fabric 环境**

1. 在 GOPATH目录下 ，创建源代码目录,下载源代码。操作如下：
```shell
mkdir -p $GOPATH/src/github.com/hyperledger
```
 
2. 切换到目录，下载 Hyperldger Fabric 源代码。操作如下：

```shell
cd $GOPATH/src/github.com/hyperledger
git clone https://github.com/hyperledger/fabric.git
```


3. 国内外服务器问题：下载缓慢问题处理

（若是国内服务器，务必看提示。若是国外服务器请忽略提示）
提示：下载过程中如果是国内服务器，会存在下载慢的情况。主要原因是因为有两个压缩文件下载慢。若要是国内服务器，需要提前下载好两个压缩文件，然后创建对应目录，复制到对应目录，然后启动下载脚本。操作如下: 
```shell
// 1. 下载 两个压缩文件。
xxxx

// 2. 切换到github.com/hyperledger/fabric/scripts/ 目录下
cd $GOPATH/src/github.com/hyperledger/fabric/scripts/

// 3. 创建目录文件
mkdir  fabric-samples/

// 4. 复制下载好的两个压缩包至目录 fabric-samples
cp -r  xxx  ./fabric-samples/
```

5. 下载 Hyperldger Fabric 所需镜像文件

```shell
// 1. 切换到github.com/hyperledger/fabric/scripts/ 目录下
cd $GOPATH/src/github.com/hyperledger/fabric/scripts/

// 2. 下载镜像，启动bootstrap.sh 
./bootstrap.sh
//下载时间会比较长

//下载完成之后， 验证下。
docker images
```
 
6. 拷贝所需工具至 bin 目录

```shell
// 1. 切换到$GOPATH/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin目录下，将这些二进制文件拷贝到 /usr/local/bin 目录下
sudo cp *     /usr/local/bin
```
### 5. **测试**

1. 修改配置文件参数：因为是阿里云部署，需修改一个参数支持

```shell
//切换到案例目录
> cd $GOPATH/src/github.com/hyperledger/fabric/scripts/fabric-samples/first-network/
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019072911410999.png)
需修改文件：
 - docker-compose-cli.yaml 
 - base/peer-base.yaml

需修改内容：
2. 在 base/peer-base.yaml 的peer-base和orderer-base所属的environment下面添加 
```shell
- GODEBUG=netdns=go
```
3. 在 docker-compose-cli.yaml的cli下的environment添加

```shell
- GODEBUG=netdns=go
```
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190729114336475.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Jvc3MyOTY3,size_16,color_FFFFFF,t_70)

4. 启动测试案例测试是否安装成功
```shell
//切换到案例目录
> cd $GOPATH/src/github.com/hyperledger/fabric/scripts/fabric-samples/first-net
work/
//生成证书
> ./byfn.sh generate
// 这一步会生成公私钥、 证书、 cryptogen、 configtx、 创世区块、 锚节点等
> ./byfn.sh up
//这步执行结束后后显示
![](images/screenshot_1563519892342.png)
//说明环境正常， 执行停止命令， 删除测试网络
> ./byfn.sh down
```
注：一定要执行down命令删除网络

正常操作执行 ./byfn.sh up ，正常会显示如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019072911485390.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Jvc3MyOTY3,size_16,color_FFFFFF,t_70)

至此，一个简单的 Hyperldger Fabric1.4基础运行网络环境搭建正常了。

