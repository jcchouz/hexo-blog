---
title: BigData-2-HBase下载、安装、配置
date: 2018-11-28 13:46:13
tags:
  - BigData（大数据）
  - HBase
categories:
  - [BigData（大数据）, HBase]
---
#### 0 参考网址
> https://blog.csdn.net/bd_ai_iot/article/details/78287379

---

#### 1 下载
`wget https://mirrors.tuna.tsinghua.edu.cn/apache/hbase/stable/hbase-1.4.8-bin.tar.gz`

> HBase的官网是这个`http://www.apache.org/dyn/closer.cgi/hbase/`

退出容器，但不关闭`ctrl+P+Q`

改为root用户`su root`，输入密码

拷贝安装包`docker cp 333a:/opt/hbase-1.4.8-bin.tar.gz /home1/jupyter_data/ZhouJiacheng/`

进入容器`docker exec -it 333a /bin/bash`
<!-- more -->
#### 2 解压
`tar -zxvf /opt/hbase-1.4.8-bin.tar.gz -C /opt`

#### 3 设置用户和用户组
**以root用户执行!**

设置hbase-1.4.8所在用户为hadoop `chown -R hadoop /opt/hbase-1.4.8`

设置hbase-1.4.8所在用户组为hadoop `chown -R hadoop:hadoop /opt/hbase-1.4.8`

#### 4 设置环境变量
**以hadoop用户执行!**

`vi /home/hadoop/.bashrc`

加上:
```
HBASE_HOME=/opt/hbase-1.4.8
export HBASE_HOME
export HBASE_CONF_DIR=$HBASE_HOME/conf
export HBASE_CLASS_PATH=$HBASE_CONF_DIR
PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$SPARK_HOME/bin:$SPARK_HOME/sbin:$JAVA_HOME/bin:$HBASE_HOME/bin:$PATH
```

执行一下.bashrc文件 `source ~/.bashrc`

验证是否成功`hbase version`

成功则显示：![](https://i.loli.net/2018/11/28/5bfe3a98dd4fe.jpg)

#### 5 HBase配置——hbase-env.sh

进入hbase目录中的conf目录`cd $HBASE_HOME/conf/`

编辑hbase-env.sh文件`vi $HBASE_HOME/conf/hbase-env.sh`

修改（可以采用复制一行出来，保留原有行）
+ `export JAVA_HOME=/usr/lib/jvm/java-8-oracle/`
+ `export HBASE_CLASSPATH=/opt/hbase-1.4.8/conf`
+ `export HBASE_MANAGES_ZK=true`(使用HBase自带的zookeeper)
+ `export HBASE_LOG_DIR=/opt/hbase-1.4.8/logs`

#### 6 HBase配置——hbase-site.xml
编辑hbase-site.xml文件`vi $HBASE_HOME/conf/hbase-site.xml`

```
<configuration>
    <property>
        <name>hbase.rootdir</name>
        <value>hdfs://hadoop1:9000/hbase</value>
    </property>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    <property>
        <name>hbase.cluster.distributed</name>
        <value>true</value>
    </property>
</configuration>
```

#### 7 开启HBase
`start-hbase.sh`

> 1、启动hbase输出ignoring option PermSize=128m; support was removed in 8.0告警信息
> 解决办法：由于JDK使用的是jdk1.8.0_65，注释掉以下：
```
# Configure PermSize. Only needed in JDK7. You can safely remove it for JDK8+
export HBASE_MASTER_OPTS="$HBASE_MASTER_OPTS -XX:PermSize=128m -XX:MaxPermSize=128m"
export HBASE_REGIONSERVER_OPTS="$HBASE_REGIONSERVER_OPTS -XX:PermSize=128m -XX:MaxPermSize=128m"
```

> 2、启动时输出: regionserver running as process 1258. Stop it first.
> 则运行  
> `stop-all.sh`  
> 再尝试开启

查看进程`jps`
![](https://i.loli.net/2018/11/28/5bfe415fad5a0.jpg)

进入HBase shell`hbase shell`

退出进入HBase shell`exit`

停止HBase`stop-hbase.sh`

停止regionserver`/opt/hbase-1.4.8/bin/hbase-daemon.sh stop regionserver RegionServer`

#### 8 制作docker镜像

1. 在容器里
    + `stop-hbase.sh`
    + `stop-all.sh`
2. 退出容器，但不关闭`ctrl+P+Q`
3. 制作镜像`docker commit -m "hbase install" 333a ubuntu:hbase`

#### 9 设置端口映射

制作镜像

再新开一个容器，增加端口16010映射

`docker run -itd -p 50070:50070 -p 8088:8088 -p 16010:16010 -p 9000:9000 -p 2181:2181 -p 16000:16000 ubuntu:hbase`

浏览器访问`http://202.204.62.145:16010`

#### 10 新开容器后要做的事

1. `su hadoop`
2. 尝试`ssh hadoop1`，若不成功，原因可能有：
    1. 若提示连接不了22端口，则ssh未打开，解决办法：  
        + 切换到root用户`su root`
        + 启动ssh`service ssh start`
        + 验证是否启动`ps -e | grep ssh`
    2. 若提示找不到hadoop1的host，解决办法：
        ```
        cat>>/etc/hosts<<EOF  
        #Hadoop Name Node  
        $当前容器ip  hadoop1  
        EOF
        ```
3. ssh成功后，再开启hadoop和hbase