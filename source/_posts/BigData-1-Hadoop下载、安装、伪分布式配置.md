---
title: BigData-1-Hadoop下载、安装、伪分布式配置
date: 2018-11-27 15:41:01
tags:
  - BigData（大数据）
  - Hadoop
  - Linux
categories:
  - [BigData（大数据）, Hadoop]
---
首先145服务器有：带java的ubuntu镜像

#### 1 创建容器
`docker run -itd -p 50070:50070 -p 8088:8088 ubuntu:java`

#### 2 进入容器
`docker exec -it 333a /bin/bash`

#### 3 配置/etc/hosts文件

```
cat>>/etc/hosts<<EOF  
#Hadoop Name Node  
172.17.0.3  hadoop1  
EOF
```
<!-- more -->
设置开机自动配置

+ 切换到root用户`su root`，输入密码
+ `vi ~/.bashrc`
+ 在最后加上
```
cat>>/etc/hosts<<EOF  
#Hadoop Name Node  
172.17.0.3  hadoop1  
EOF
```
+ 执行一下.bashrc文件 `source ~/.bashrc`

#### 4 设置root密码
`passwd root`

***密码：root**

#### 5 创建hadoop用户，并设置密码
`useradd -m hadoop`

`passwd hadoop`

密码：`hadoop123`

*删除用户`userdel -r hadoop`

#### 6 ubuntu查看用户列表
`cat /etc/passwd`

#### 7 创建HADOOP安装目录
```
mkdir /opt/hadoop-2.7.7
chown -R hadoop.hadoop /opt/hadoop-2.7.7
ln -s /opt/hadoop-2.7.7 /opt/hadoop
chown -R hadoop.hadoop /opt/hadoop
```

#### 8 为hadoop用户设置shell限制
```
cat>>/etc/security/limits.conf<<EOF
hadoop soft nproc 2047
hadoop hard nproc 16384
hadoop soft nofile 1024
hadoop hard nofile 65536
EOF
```
```
cat>>/etc/pam.d/login<<EOF
session    required     pam_limits.so
EOF
```

#### 9 配置Hadoop用户的环境文件
```
cd
```
```
cat > ~hadoop/.bashrc <<EOF
# .bashrc

# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi

# User specific aliases and functions

JAVA_HOME=/usr/lib/jvm/java-8-oracle
export JAVA_HOME

HADOOP_HOME=/opt/hadoop
export HADOOP_HOME

HADOOP_CONF_DIR=/opt/hadoop/etc/hadoop
export HADOOP_CONF_DIR

export SPARK_HOME=/opt/spark

PATH=\$HADOOP_HOME/bin:\$HADOOP_HOME/sbin:\$SPARK_HOME/bin:\$SPARK_HOME/sbin:\$JAVA_HOME/bin:\$PATH
export PATH

EOF
```

#### 10 安装Hadoop2.7.7软件

从145服务器中拷过来安装包

或者下载`wget https://www-eu.apache.org/dist/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz`

退出容器，但不关闭`ctrl+P+Q`

改为root用户`su root`，输入密码

拷贝安装包`docker cp /home1/jupyter_data/ZhouJiacheng/hadoop-2.7.7.tar.gz 333a:/opt/`

进入容器`docker exec -it 333a /bin/bash`

解压(-c 表示解压到指定目录)`tar -zxvf /opt/hadoop-2.7.7.tar.gz -C /opt/`

#### 11 配置ssh本机无密码登录
1. root用户下

+ 先执行`apt-get update`

+ 再执行`apt-get install openssh-server`

+ 开启ssh`service ssh start`

+ 检测ssh是否启动`ps -e | grep ssh` 

>有sshd,说明ssh服务已经启动，如果没有启动，则输入上面的开启ssh命令

+ 彻底卸载ssh (--purge 指明这个包裹应该被完全清除)`apt-get --purge remove openssh-server`

2. 切换到**hadoop用户！**

+ `cd`

+ `ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa`

+ `cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys`

+ `chmod 600 ~/.ssh/authorized_keys`

3. 以hadoop用户身份，验证本机的无密码ssh **（必须执行以下验证！）**

+ `ssh hadoop1`  
> 出现下图为正确
> 
> ![](https://i.loli.net/2018/11/25/5bfa3f06cda6c.jpg)

+ `ssh localhost` **正确结果同上*  

+ `ssh 0.0.0.0` **正确结果同上*   

+ `ssh 127.0.0.1` **正确结果同上*  

4. 设置ssh自动启动

切换到root用户`su root`，输入密码

vi `~/.bashrc`

在最后加上`service sshd start`，保存

执行一下.bashrc文件 `source ~/.bashrc`

#### 12 配置单节点伪分布式（Pseudo-Distributed）Hadoop环境

1. 配置文件/etc/hadoop/core-site.xml

+ `cd /opt/hadoop-2.7.7`

+ `vi /opt/hadoop-2.7.7/etc/hadoop/core-site.xml`

+ 
```
<configuration>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/home/hadoop/dfs/temp</value>
        <description>A base for other temporary directories.</description>
    </property>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://hadoop1:9000</value>
    </property>
    <property>
        <name>io.file.buffer.size</name>
        <value>131072</value>
        <description>io file buffer size</description>
    </property>
     <property>
        <name>hadoop.proxyuser.hadoop.hosts</name>
        <value>*</value>
     </property>
     <property>
        <name>hadoop.proxyuser.hadoop.groups</name> 
        <value>*</value>
     </property>
</configuration>
```

2. 配置文件etc/hadoop/hdfs-site.xml

+ `cd /opt/hadoop-2.7.7`

+ `vi /opt/hadoop-2.7.7/etc/hadoop/hdfs-site.xml`

+ 
```
<configuration>
    <property>
        <name>dfs.namenode.name.dir</name>     
        <value>file:/home/hadoop/dfs/name</value>     
    </property> 
    <property> 
        <name>dfs.datanode.data.dir</name>      
        <value>file:/home/hadoop/dfs/data</value>
    </property>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    <property>
        <name>dfs.webhdfs.enabled</name>
        <value>true</value>
    </property>
    <property>
        <name>dfs.http.address</name>
        <value>hadoop1:50070</value>
    </property>
    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>hadoop1:50090</value>
    </property>

</configuration>
```

3. 配置文件etc/hadoop/slaves

`cd /opt/hadoop-2.7.7`

`vi /opt/hadoop-2.7.7/etc/hadoop/slaves`

增加`hadoop1`

4. 创建HDFS使用的Linux目录

切换到hadoop用户`su hadoop`

`mkdir -p /home/hadoop/dfs/name`

`mkdir -p /home/hadoop/dfs/data`

`mkdir -p /home/hadoop/dfs/temp`

> 使用 -p 参数来创建多级文件夹

5. 格式化文件系统

`hdfs namenode -format`

6. 启动HDFS

`start-dfs.sh`

> 如果报错`hadoop1: Error: JAVA_HOME is not set and could not be found.`，那么：  
> 1. 输入`java –version`，查看jdk是否安装成功
> 2. 输入`export`，查看jdk环境变量是否设置成功![](https://i.loli.net/2018/11/25/5bfa45d5a2a7d.jpg)
> 3. 在`/opt/hadoop/etc/hadoop/hadoop-env.sh`中，再显式地重新声明一遍JAVA_HOME：  
将  
`export JAVA_HOME=$JAVA_HOME`     
修改为  
`export JAVA_HOME=/usr/lib/jvm/java-8-oracle`

再尝试开启`start-dfs.sh`

出现下图为正确：
![](https://i.loli.net/2018/11/25/5bfa482124e9b.jpg)

7. 浏览NameNode的web接口

浏览器访问 http://202.204.62.145:50070/

看到界面：
![](https://i.loli.net/2018/11/25/5bfa4894b40bf.jpg)

---
**注意以下所有测试命令都要在hadoop用户下执行！！！！！**

8. 查看HDFS启动后的情况
`jps`

前面的数字是jvm的进程id，后面是jvm的类启动信息

9. 在HDFS上创建目录

`hdfs dfs -mkdir /user`

`hdfs dfs -mkdir /user/hadoop`

10. 测试

`cd /opt/hadoop-2.7.7`

`hdfs dfs -put etc/hadoop input`

`hadoop jar /opt/hadoop-2.7.7/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.7.jar grep input output 'dfs[a-z.]+'`

`hdfs dfs -get output output`

`hdfs dfs -cat output/*`

再清除测试数据

`rm -rf output`

`hdfs dfs -rm -r /user/hadoop/input`

`hdfs dfs -rm -r /user/hadoop/output`

11. HDFS集群管理命令

查看集群状态`hdfs dfsadmin -report`

查看文件块组成`hdfs fsck / -files -blocks`

12. 停止HDFS

`stop-dfs.sh`

#### 13 配置单节点上的YARN

1. 配置文件/opt/hadoop-2.7.7/etc/hadoop/mapred-site.xml

+ `cd /opt/hadoop-2.7.7/etc/hadoop/`

+ `cp mapred-site.xml.template mapred-site.xml`

+ `vi /opt/hadoop-2.7.7/etc/hadoop/mapred-site.xml`

+ 
```
<configuration>
    <property>
       <name>mapreduce.framework.name</name>
       <value>yarn</value>
    </property>
    <property>
       <name>mapreduce.jobhistory.address</name>
       <value>hadoop1:10020</value>
    </property>
    <property>
       <name>mapreduce.jobhistory.webapp.address</name>
       <value>hadoop1:19888</value>
    </property>
</configuration>
```

2. 配置文件/opt/hadoop-2.7.7/etc/hadoop/yarn-site.xml

+ `cd /opt/hadoop-2.7.7`

+ `vi /opt/hadoop-2.7.7/etc/hadoop/yarn-site.xml`

+ 
```
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <property>
        <name>yarn.nodemanager.auxservices.mapreduce.shuffle.class</name>
        <value>org.apache.hadoop.mapred.ShuffleHandler</value>
    </property>
    <property>
        <name>yarn.resourcemanager.scheduler.address</name>
        <value>hadoop1:8030</value>
    </property>
    <property>
        <name>yarn.resourcemanager.resource-tracker.address</name>
        <value>hadoop1:8031</value>
    </property>
    <property>
        <name>yarn.resourcemanager.address</name>
        <value>hadoop1:8032</value>
    </property>
    <property>
        <name>yarn.resourcemanager.admin.address</name>
        <value>hadoop1:8033</value>
    </property>
    <property>
        <name>yarn.resourcemanager.webapp.address</name>
        <value>hadoop1:8088</value>
    </property>
    <property>
        <name>yarn.nodemanager.vmem-pmem-ratio</name>
        <value>2.5</value>
    </property>

</configuration>
```

3. 启动YARN

`start-yarn.sh`

> 出现如下提示  
> `localhost: nodemanager running as process 4671. Stop it first.`  
> `hadoop1: nodemanager running as process 4671. Stop it first.`  
> 的解决办法:执行`stop-all.sh`  
> 再尝试执行`start-yarn.sh`

启动YARN后，再启动HDFS `start-dfs.sh`

4. 查看启动后的情况

`jps`

> 结果
> ![](https://i.loli.net/2018/11/25/5bfa5ce96f0a1.jpg)

5. 浏览ResourceManager的web接口

浏览器访问 http://202.204.62.145:8088/

> 结果
> ![](https://i.loli.net/2018/11/25/5bfa5d39b3c88.jpg)

6. 停止YARN

`stop-yarn.sh`

#### 14 测试：运行示例程序

1. 查看可用的实例列表

`cd /opt/hadoop/share/hadoop/mapreduce`

`yarn jar ./hadoop-mapreduce-examples-2.7.7.jar`

2. 例子1 计算PI

运行带有2个map和10000000个样本的pi实例

`cd /opt/hadoop/share/hadoop/mapreduce`

`yarn jar ./hadoop-mapreduce-examples-2.7.7.jar pi  2 10000000`

3. 例子2 运行Terasort测试

(1) 运行teragen生成随机数据用于排序

`cd /opt/hadoop/share/hadoop/mapreduce`

`yarn jar hadoop-mapreduce-examples-2.7.7.jar teragen 10000 /user/terasort`

> 10000表示10000KB=10MB

注意再次进行实验(1)需要删除/user/terasort

`hdfs dfs -ls /user`

`hdfs dfs -rm  -r  /user/terasort`

(2) 运行terasort对数据进行排序

创建结果目录 `hdfs dfs -mkdir  /user/terasortResult`

`cd /opt/hadoop/share/hadoop/mapreduce`

`yarn jar hadoop-mapreduce-examples-2.7.7.jar terasort \`

`/user/terasort   /user/terasortResult`

(3) 验证排序后的teragen

`cd /opt/hadoop/share/hadoop/mapreduce`

`yarn jar hadoop-mapreduce-examples-2.7.7.jar teravalidate \`

`/user/terasortResult /user/terasortvalidate`

(4) 删除测试用目录

`hdfs dfs -rm  -r  /user/terasort`

`hdfs dfs -rm  -r  /user/terasortResult`

`hdfs dfs -rm  -r  /user/terasortvalidate`

4. 例子3 DFS IO基准测试

在写模式下运行名为TestDFSIO的HDFS基准测试应用程序:
运行带有2个128MB文件

`cd /opt/hadoop/share/hadoop/mapreduce`

`yarn jar hadoop-mapreduce-client-jobclient-2.7.7-tests.jar TestDFSIO \`

`-write -nrFiles 2 -fileSize 128`

在读模式下运行TestDFSIO
`cd /opt/hadoop/share/hadoop/mapreduce`

`yarn jar hadoop-mapreduce-client-jobclient-2.7.7-tests.jar TestDFSIO \`

`-read -nrFiles 2 -fileSize 128`

清理刚才的数据
`cd /opt/hadoop/share/hadoop/mapreduce`

`yarn jar hadoop-mapreduce-client-jobclient-2.7.7-tests.jar TestDFSIO \`

`-clean`

#### 15 制作docker镜像

1. 在容器里
    + `stop-all.sh`
2. 退出容器，但不关闭`ctrl+P+Q`
3. 制作镜像`docker commit -m "hadoop install" 333a ubuntu:hadoop`