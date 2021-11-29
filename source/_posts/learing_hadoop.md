---
title: 虚拟环境下Hadoop集群测试运行WordCount
date: 2021-11-20 18:48:34
tags: 
  - 虚拟机
  - 大数据
  - 学习
  - Hadoop
categories:
  - 学习笔记
  - 大数据笔记
description: 卑微上学人应付大数据课程设计搭建了一个虚拟的Hadoop集群跑了一下WordCount
cover: https://cdn.jsdelivr.net/gh/Kenguo05/blog-img@main/img/blog-img/Hadoop.jpg
---

## 大数据课设天降

离大谱了属于是，什么牛马大数据突然整个课程设计，明明上课的时候老师比学生还摆，要不是为了学分爷才不做。。。

课设内容是选择一个大数据系统，在虚拟化环境中搭建两个节点以上的集群部署系统并挑选一个应用进行测试。

于是俺就搭了个三节点集群，节点上运行 [CentOS 7](https://www.centos.org/)，部署 [Hadoop](http://hadoop.apache.org/)，随便跑了个WordCount应付了一下，这里做个简单复盘

**PS：由于以前也没接触过这种东西，基本上都是一边学一边查一边做的，就。。可能有很多沙雕问题，果咩**

## 虚拟化环境的搭建

虚拟机的话，选择了 [VMware](https://www.vmware.com/)，这个应该是挺多人都在使用的虚拟机软件了，使用起来也挺简单的。对于这种需要搭建多节点集群的课题，选择 VMware 家的软件记得要选那个 [Workstation Pro](https://www.vmware.com/products/workstation-pro.html)，还有一个叫 Workstation Player 的功能比较少，好像不支持多节点集群。

软件下好之后要准备操作系统，这里选择了 CentOS 7，这个应该用的人也挺多的，Ubuntu 也不错，不过我没用过，好像一些命令有区别，不过应该差不多，下载的话下载那个 x86_64 的 iso 文件，回头建立虚拟机的时候把映像文件路径指向这个文件就好了。

虚拟机的搭建挺简单的，网上教程很多，我不赘述了（就是懒，开摆！

## 网络配置

由于要让节点之间能够通讯，这里需要配置 Linux 的网卡文件，登录 root 用户，输入命令

```sh
cd /etc/sysconfig/network-scripts
```

进入该目录，输入 `ls` 查看目录文件，应该可以看到一个文件名为`ifcfg-ens32` 的文件，使用 `vim` 编辑它（**注意：这个文件名中的数字不同机器可能不同，我这里是32，也有可能是33或者34啥的，反正就编辑这个 `ifcfg-ens数字` 的文件就好**

```sh
vi ifcfg-ens32
```

修改几个条目

```
BOOTPROTO=static # 这个默认值应该是dhcp
ONBOOT=yes # 这个默认值应该是no
```

再加几个条目

```
IPADDR=192.168.137.101 
# 这里的ip要根据自己的情况配置，前三个数字可以在VMware的虚拟网络编辑器中查看，注意是NAT项中对应的网段，最后一个数字可以随便填，注意不同节点应该不同
GATEWAY=192.168.137.2
# 这个看虚拟网络编辑器的NAT的相关设置中查看网关，要和那里面的数字一样
DNS1=192.168.137.2
# 这个和上面一条一样
```

修改完后，重启网络服务让其生效

```sh
service network restart
```

可以通过 `ipaddr` 或者 `ifconfig` 命令查看 ip 是否配置成功，其中有时 `ifconfig` 需要安装网络工具后才能使用，可以用 `yum` 安装

```sh
yum install net-tools
```

就可以使用了。

下一步修改主机名，方便集群搭建后区分各个主机，进入目录 `/etc`下，修改 `hostname` 文件

```sh
cd /etc
vi hostname
```

将内容修改成

```
hadoopmaster
```

当然可以随意起名字，只要自己确认节点之间的关系就好了，如果是从节点，可以修改为 `hadoopslave` ，怎么方便怎么来。

修改 `hosts` 文件，增加条目

```
192.168.137.101 hadoopmaster
192.168.137.102 hadoopslave1
192.168.137.103 hadoopslave2
```

三节点都这么配置，这样节点之间相互通讯的时候就不用 ip 了，用主机名更容易让人理解。

还需要关闭防火墙

```sh
systemctl stop firewalld
systemctl disable firewalld
```

为了方便之后的操作，可以新建一个 `hadoop` 用户

```sh
useradd -d /home/hadoop hadoop
# 新建用户
passwd hadoop
# 修改密码
```

然后赋予该用户 `root` 权限，到时候 Hadoop 系统的运行都将在这个用户下完成

```sh
vi /etc/sudoers
```

在文件后追加以下内容

```
hadoop    ALL=(ALL)    ALL
```

完成。

## 安装 JDK

Hadoop 是基于 Java 的大数据系统，所以需要配置 Java 环境，这里选择使用 [Xftp](https://www.netsarang.com/zh/xftp/) 直接上传 Java 安装包到虚拟机，这个公司做了不少远程开发用的软件，我平时用的比如命令行工具 Xshell，文件传输工具 Xftp，这些软件个人使用是完全免费的，非常良心，可以去人家 [NetSarang 官网](https://www.netsarang.com/zh/)看看。先去 [Java 官网](https://www.oracle.com/java/technologies/downloads/#java8)准备好 Java 的 Linux 安装包，这里选择使用经典的 Java 8，在 Xftp 中通过之前配置的 ip 地址远程连接到虚拟机，用 root 用户登录，选择一个合适的目录，我选择了 `/root/apps`，将安装包直接从本地拖到目录下，然后再虚拟机中进入相应目录，可以查看到该安装包，解压它

```sh
tar -zxvf jdk-8u311-linux-x64.tar.gz -C /root/apps
# 第三个字符串是自己下的安装包的名字，最后一个是解压到的目录
```

和 Windows 系统中一样，需要配置 Java 环境变量，输入命令

```sh
vi /etc/profile
```

在文件最后加入

```
export JAVA_HOME=/root/apps/jdk1.8.0_311
export PATH=$PATH:$JAVA_HOME/bin
```

重新加载环境变量

```sh
source /etc/profile
```

检测是否配置成功，输入

```sh
java -version
```

若打印 Java 版本号，则说明配置成功。

## 配置免密登录

由于 Hadoop 需要将任务发布到各个节点，有必要配置 ssh 免密登录，保证相互之间能够方便的登录，在三台机器上分别输入

```sh
su hadoop 
# 切换到hadoop用户
cd ~
# 进入用户目录
ssh-keygen
# 生成密钥
```

然后不管输出什么，一路回车，直到生成完成。生成完成后，在 `hadoop` 用户目录下将生成一个 `.ssh` 文件夹，文件夹中存放了密钥与公钥，现在需要建立主机之间的信任关系实现免密登录，输入命令

```sh
ssh-copy-id hadoopslave1
ssh-copy-id hadoopslave2
# 这里根据自己的主机名修改
```

**最后还需要建立自己对自己的免密登录，命令同理**

保证三台主机能够相互免密登录即可，可以这样验证

```sh
ssh hadoopslave1
```

若成功免密登录，则配置成功。

## 安装部署 Hadoop

去 [Apache Hadoop 官网](https://hadoop.apache.org/releases.html)下载安装包，这里可以选择 `binary` 版本。~~当然如果是硬核大佬可以下载 `source` 版的自行编译（逃~~

### 安装 Hadoop

连接其中一个主机，通过 Xftp 将 Hadoop 上传到特定目录下，比如我上传到了 `/usr/scr` 下，然后执行以下命令解压至特定目录下

```sh
tar -zxvf hadoop-3.3.1.tar.gz -C /home/hadoop/apps
# 这里表示将安装包解压至/home/hadoop/apps目录下
```

解压后在 `/home/hadoop/apps` 目录下可以看到 Hadoop 的文件夹。

### 修改 Hadoop 配置文件

Hadoop 的配置文件存放在 `hadoop-3.3.1/etc/hadoop` 目录下

#### 修改 hadoop-env.sh

在文件中加入以下内容

```sh
export JAVA_HOME=/root/apps/jdk1.8.0_311
# 这里写自己JAVA_HOME的路径
```

#### 修改 core-site.xml

在文件的 `<configuration>` 标签中加入

```xml
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://hadoopmaster:9000</value>
    <description>用于指定namenode</description>
</property>
<property>
	<name>hadoop.tmp.dir</name>
    <value>/home/hadoop/hadoopdata</value>
    <description>用于指定Hadoop运行产生的数据存放的目录</description>
</property>
```

#### 修改 hdfs-site.xml

在文件的 `<configuration>` 标签中加入

```xml
<property>
	<name>dfs.namenode.name.dir</name>
    <value>/home/hadoop/hadoopdata/name</value>
    <description>用于指定namenode的数据存储目录</description>
</property>
<property>
	<name>dfs.datanode.data.dir</name>
    <value>/home/hadoop/hadoopdata/data</value>
    <description>用于指定datanode的数据存储目录</description>
</property>
<property>
	<name>dfs.replication</name>
    <value>2</value>
    <description>用于指定HDFS数据副本存储数</description>
</property>
<property>
	<name>dfs.secondary.http.address</name>
    <value>hadoopslave1:50090</value>
    <description>用于指定secondarynamenode及其访问端口，是与namenode不同的节点</description>
</property>
```

#### 修改mapred-site.xml

在文件的 `<configuration>` 标签中加入

```xml
<property>
    <name>mapreduce.framwork.name</name>
    <value>yarn</value>
    <description>用于指定yarn为资源调度框架</description>
</property>
```

**这里其实还有一段要写，我刚开始运行的时候，提示报错，无法加载 classpath ，解决方法如下**

通过命令

```sh
hadoop classpath
```

显示完整的 classpath ，这里将打印一大串路径，将其复制下来，回到 `mapred-site.xml` 文件，在文件的 `<configuration>` 标签中加入

```xml
<property>
    <name>yarn.app.mapreduce.am.env</name>
    <value>刚刚复制的路径</value>
</property>
<property>
    <name>mapreduce.map.env</name>
    <value>刚刚复制的路径</value>
</property>
<property>
    <name>mapreduce.reduce.env</name>
    <value>刚刚复制的路径</value>
</property>
```

#### 修改 yarn-site.xml

在文件的 `<configuration>` 标签中加入

```xml
<property>
	<name>yarn.resourcemanager.hostname</name>
	<value>hadoopmaster</value>
    <description>用于指定resourcemanager，与namenode不同或相同好像都行</description>
</property>
<property>
	<name>yarn.nodemanager.aux-services</name>
	<value>mapreduce_shuffle</value>
    <description>用于指定yarn集群为mapreduce程序提供的shuffle服务</description>
</property>
```

#### 修改 workers 文件

在文件中加入以下内容，指定从节点

```
hadoopmaster
hadoopslave1
hadoopslave2
```

**注意：老版本的 Hadoop 中可能没有 workers 文件，但是有一个 slaves 文件，这两个文件有哪个就在那个文件章加入上面的内容**

### 部署 Hadoop 至集群

输入以下命令将 Hadoop 安装目录同步至另外两个节点

```sh
scp -r hadoop-3.3.1/ hadoop@hadoopslave1:/home/hadoop/apps
scp -r hadoop-3.3.1/ hadoop@hadoopslave2:/home/hadoop/apps
```

修改各个节点的环境变量， 在 `/etc/profile` 添加

```
export HADOOP_HOME=/home/hadoop/apps/hadoop-3.3.1
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

重新加载环境变量

```sh
source /etc/profile
```

通过 `hadoop --help` 测试配置是否成功

### 初始化 namenode

在先前配置的 namenode 上执行命令

```sh
hadoop namenode -format
```

若输出信息打印 `successfully formatted` ，即说明初始化成功，检查 `hadoop` 目录下应多出一个 `hadoopdata` 目录

## 启动 Hadoop集群

顺利到达这一步，胜利近在眼前，好耶！~~其实后面又出了一堆bug调试了大半天~~

### 启动 HDFS 

在任意节点上执行命令

```sh
start-dfs.sh
```

启动完成后，在各个节点上通过 `jps` 命令检查进程，根据以上的配置，查询到的进程应为

- hadoopmaster 上

  存在 namenode 、datanode、jps 三类进程

- hadoopslave1 上

  存在 secondarynamenode 、datanode、jps 三类进程

- hadoopslave2 上

  存在 datanode、jps 两类进程

### 启动 yarn

在 resourcemanager 节点上执行命令

```sh
start-yarn.sh
```

启动完成后，在该节点上可以查询到 resourcemanager 进程

## 尝试运行 WordCount

准备一份文本文件，上传至任意节点，通过命令

```sh
hadoop fs -put test.txt /input
```

把节点上的 `test.txt` 文件上传到 hdfs 系统的 `/input` 目录下

执行命令

```sh
hadoop jar /home/hadoop/apps/hadoop-3.3.1/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.1.jar wordcount /input /output
```

表示对 `/input` 目录下的文件执行 WordCount 应用，将结果输出到 `/output` 目录下

执行成功后，通过浏览器访问名节点的 9870 端口，可以进入文件系统，在 `/output` 目录下可以看到一个 `part-r-00000` 文件，这就是结果文件。

至此，整个节点的搭建与部署完成，之后就只要找点测试文件测一测，~~摆大烂了~~。

## 总结

写这么篇博客也就是复盘一下课设整个过程，虽然大数据课老师基本啥也没讲，布置这么一个作业实在是很啥比，但 Hadoop 还是很有用的，记录一下万一以后用到了呢。
