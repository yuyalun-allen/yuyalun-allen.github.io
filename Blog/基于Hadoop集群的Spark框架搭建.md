# 基于Hadoop集群的Spark框架搭建

## 准备

### WSL
虽然Hadoop支持Windows作为开发平台，但是需要Cywin（类Unix环境）作为运行环境。
Linux是Hadoop框架的最优选择！那么问题来了，怎样获得一个Linux系统呢？

双系统/虚拟机/子系统。
当然是WSL啦！WSL是Windows Subsystem Linux的缩写，也就是Windows的Linux子系统，区别于双系统和虚拟机，WSL可以自由地调用Windows系统的任何资源。也就是不用为WSL分配任何CPU、内存、硬盘资源，你的就是我的。

从冯诺依曼机的角度理解WSL，本质上就是在硬盘上有一块空间存放Linux系统，且可以通过系统调用直接执行Linux指令。

WSL是我之前就配置好的，这里就不赘述了，想要了解的同学可以参照[官方文档](https://learn.microsoft.com/zh-cn/windows/wsl/install)。

### Java
Hadoop框架由Java编写，运行在Java环境中，且为Java提供了API。因此在安装Hadoop前需要先安装Java虚拟机。

Ubuntu可以通过`sudo apt install openjdk-11-jdk`安装Java集成开发工具，若不利用Java进行编程也可以仅通过`sudo apt install openjdk-11-jre`安装Java运行时环境，jre中处理包含jvm虚拟机和lib类库。

下一步需要将jdk目录下的bin加入path，操作为`sudo vim /etc/profile`打开profile文件，设置Java Home变量`JAVA_HOME=/usr/lib/jvm/openjdk-11-jdk-amd64`，编辑Path变量`PATH=$JAVA_HOME/bin:$PATH`。用`source /etc/profile`激活更改，并用`echo $PATH`确认Path变量内容。

Ubuntu默认的apt安装位置根据文件类型不同分为`/usr/share`、`/usr/lib`、`/usr/bin`。为了查看JDK的具体安装位置，可以使用命令`which java`或`whereis java`。

### Scala
Spark框架由Scala编写，运行时需要Scala环境支持，因此需要安装Scala环境。

安装方式和Java一致，分为两步：apt install；加入PATH变量。此处不再赘述。

### SSH
为了使用管理Hadoop远程进程的脚本，ssh（secure shell protocal）程序必须被安装。Hadoop是分布式平台，多个机器协作时使用免密登录可以减少每次登录主机输入密码的繁琐。

```
sudo apt install openssh-server
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
service sshd restart
ssh localhost
```

ssh采用一种非对称加密技术，工作过程大致如下所述：
1. 本地向远程服务端发起连接
2. 服务端随机生成一个字符串发送给发起登录的本地端
3. 本地对该字符串使用私钥（~/.ssh/id_rsa）加密发送给服务端
4. 服务端使用公钥（~/.ssh/id_rsa.pub）对私钥加密后的字符串进行解密
5. 服务端对比解密后的字符串和第一次发送给客户端未加密的字符串，若一致则判断为登录成功

## 伪分布式
### Hadoop
首先从[官网](https://dlcdn.apache.org/hadoop/common/)下载稳定版本的压缩文件,`wget -R https://dlcdn.apache.org/hadoop/common/`。当然不需要下载整个文件夹，只需要挑选源码文件和压缩文件任意一者下载即可。最好同时下载对应的asc文件和KEYS文件，通过
```
pgp -ka KEYS
pgp downloadfile.asc
```
校验文件的数字签名以保证其完整性。

接着把压缩文件解压至目标位置，这里以`/usr/local`为例，`tar -zxvf hadoop-x.x.x.tar.gz -C /usr/local`。顺手可以改个名字`mv /usr/local/hadoop-x.x.x.tar.gz /usr/local/hadoop`。

然后`vim /etc/profile`配置环境变量:
```
HADOOP_HOME=/usr/local/hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```
`source /etc/profile`使更改生效。在`$HADOOP_HOME/etc/hadoop/hadoop-env.sh`文件中显式声明JAVA_HOME，`export JAVA_HOME=/usr/local/java`

*此时单节点的Hadoop平台已经搭建完毕，单节点的平台可以用来进行环境测试。*

接下来为了实现伪分布式的Hadoop平台，我们需要配置一些xml文件`core-site.xml` `hdfs-site.xml` `yarn-site.xml` `mapred-site.xml` ，这些文件均位于`$HADOOP_HOME/etc/hadoop`文件夹下。配置参数可以参照[官网](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html)。

### Spark
Spark的搭建过程与Hadoop十分类似，故不赘述。

### 小测试
1. 启动Hadoop平台
```
sudo service ssh start
start-all.sh
```

2. 启动Spark框架

```
cd $SPARK_HOME
sudo ./sbin/start-master.sh
sudo ./sbin/start-worker.sh spark://YOGA-Allen.:7077
sudo jps
```

3. MapReduce的WordCount

```
hdfs dfs -put the_tale_of_two_cities.txt /input
hadoop jar /usr/local/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.4.jar wordcount /input /output
hdfs dfs -cat /output/*
```

## 分布式

那么问题来了，有没有可能将Spark搭建在一个真正分布式Hadoop平台上呢？
当然可以！

机关就在于刚刚配置的xml文件中。详细内容参见[官网](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/ClusterSetup.html)。

## 实验.......