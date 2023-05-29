---
title: BigData-3-使用Eclipse连接HBase（Windows）
date: 2018-11-29 14:08:47
tags:
  - BigData（大数据）
  - HBase
  - Eclipse
categories:
  - [BigData（大数据）, HBase]
  - [Eclipse]
---
#### 参考网址
> https://blog.csdn.net/bd_ai_iot/article/details/78287379

---

#### 下载hadoop-eclipse插件`hadoop-eclipse-plugin-2.7.3.jar`
> 下载地址：
> http://www.pc6.com/softview/SoftView_452492.html


#### 拷贝hadoop-2.7.7和hbase-1.4.8文件夹

1. `docker cp 62f1:/opt/hadoop-2.7.7 /home1/jupyter_data/ZJC/hadoop-2.7.7`
2. `docker cp 62f1:/opt/hbase-1.4.8 /home1/jupyter_data/ZJC/hbase-1.4.8`
3. 用WinSCP把两个文件夹考到Windows上
<!-- more -->
#### 配置环境变量
按照Windows配置环境变量的方法（右击我的电脑--->属性--->高级系统设置--->高级里的环境变量），进行配置

1. 增加`HADOOP_HOME`和`HBASE_HOME`
![](https://i.loli.net/2018/11/29/5bffa97cb29b9.jpg)
2. 在Path中增加`E:\hadoop-2.7.7`
![](https://i.loli.net/2018/11/30/5c00b79a42846.jpg)

#### 配置hosts

1. 用记事本打开`C:\Windows\System32\drivers\etc\hosts`
2. 确保`127.0.0.1       localhost`被注释
3. 在最后加入`202.204.62.145 62f169f8d9d1`
4. 保存

#### 拷贝winutils.exe文件
1. 下载winutils.exe文件
    > 下载地址：  
    > http://www.pc6.com/softview/SoftView_578664.html  
    > 选择`hadoop-2.7.1\bin\`里的winutils.exe文件
2. 将`winutils.exe`文件拷贝到`E:\hadoop-2.7.7\bin`

#### 在Eclipse中设置hadoop的根目录
1. 打开Eclipse，选中Window的Preference  
![](https://i.loli.net/2018/11/30/5c00b9897415d.jpg)
2. 点击左侧的`Hadoop Map/Reduce`，设置Hadoop的根目录为`E:\hadoop-2.7.7`  
![](https://i.loli.net/2018/11/30/5c00ba2225eac.jpg)

#### 设置插件显示和远程服务器添加
1. 按图示操作  
![](https://i.loli.net/2018/11/30/5c00baf9ce221.jpg)
2. 按图示操作  
![](https://i.loli.net/2018/11/30/5c00bb556cc3e.jpg)
3. 按图示操作（我这边已经添加了）  
![](https://i.loli.net/2018/11/30/5c00bb898e176.jpg)
4. 按图示参数配置  
![](https://i.loli.net/2018/11/30/5c00bbd12323b.jpg)
5. 点击'Finsh'，未报错则连接成功，可在左边看到'DFS locations'  
![](https://i.loli.net/2018/11/30/5c00bc22334c2.jpg)
6. 配置完成

#### 建工程，写代码测试
1. 按图示操作  
![](https://i.loli.net/2018/11/30/5c00bc96b7d2b.jpg)
2. 按图示操作  
![](https://i.loli.net/2018/11/30/5c00bcc705edc.jpg)
3. 按图示操作  
![](https://i.loli.net/2018/11/30/5c00bcf2acc5b.jpg)
4. 按图示操作  
![](https://i.loli.net/2018/11/30/5c00bd794796c.jpg)
5. 导入`E:\hbase-1.4.8\lib`目录下的所有jar包  
    ![](https://i.loli.net/2018/11/30/5c00bdfe1f8df.jpg)
    ![](https://i.loli.net/2018/11/30/5c00be229e980.jpg)
    ![](https://i.loli.net/2018/11/30/5c00be68ebf62.jpg)
    选中所有jar包，点击'打开'  
    点击'OK'
6. 在src目录下创建一个文件，文件名为
`log4j.properties`  
    注意在Map/Reduce下没法创建，解决办法：切换到Java模式  
    ![](https://i.loli.net/2018/11/30/5c00bf68477ec.jpg)
    
    点击'Java'  
    
    右键`src`，照图示操作  
    ![](https://i.loli.net/2018/11/30/5c00bf9fce41f.jpg)  
    ![](https://i.loli.net/2018/11/30/5c00c00fee779.jpg)
    
    输入内容：
    
    ```
    # Root Logger
    log4j.rootLogger=DEBUG,console,file
    log4j.appender.console=org.apache.log4j.ConsoleAppender
    log4j.appender.console.layout=org.apache.log4j.PatternLayout
    log4j.appender.console.layout.ConversionPattern=%5p [%t] - %m%n
    log4j.appender.file=org.apache.log4j.DailyRollingFileAppender
    log4j.appender.file.File=log.html
    log4j.appender.file.layout=org.apache.log4j.HTMLLayout
    ```
    
    保存
7. 新建class，写测试代码
![](https://i.loli.net/2018/11/30/5c00c0dea5e70.jpg)
![](https://i.loli.net/2018/11/30/5c00c11a51246.jpg)

写简单的建表代码：
```
package com.hbase.test;

import java.io.IOException;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.Cell;
import org.apache.hadoop.hbase.CellUtil;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.client.Admin;
import org.apache.hadoop.hbase.client.Connection;
import org.apache.hadoop.hbase.client.ConnectionFactory;
import org.apache.hadoop.hbase.client.Get;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.util.Bytes;
import org.junit.Test;

public class HbaseTest {
    public static Log log=LogFactory.getLog(HbaseInsert1.class);
    public static String al_TableName="employees";//表名
    public static String columnFamily="student_name";//列族
    public static String rowFamily="h2";//行键
    static Configuration conf=null;
    
    static{
        conf = HBaseConfiguration.create();
        conf.set("hbase.zookeeper.quorum", "62f169f8d9d1");// 使用eclipse时必须添加这个，否则无法定位master需要配置hosts
    }
    
    public static void main(String[] args) throws IOException {
    	testCreateTable();
    }
    
    /**
     * 创建表
     * HBaseAdmin 
     * HTableDescriptor
     */
    @Test
    public static void testCreateTable(){
        
        HBaseAdmin admin=null;
        HTableDescriptor htd=null;
        try {
             admin=new HBaseAdmin(conf);
            //判断要创建的表是否已经存在
            if(admin.tableExists(al_TableName)){
                System.out.println(al_TableName+"表已经存在");
                return;
            }else{
                htd=new HTableDescriptor(al_TableName.getBytes());    
                //设置列族
                htd.addFamily(new HColumnDescriptor(columnFamily));
                admin.createTable(htd);
                System.out.println(al_TableName+"表创建成功！！！");
            }
        }catch (IOException e) {
            e.printStackTrace();
        }
    }
    /**
     * 删除表
     */
    @Test
    public static void testDeletTable(){
        HBaseAdmin admin=null;
        try {
            admin=new HBaseAdmin(conf);
            if(admin.tableExists(al_TableName)){
                System.out.println("table is exits");
                //如果表存在，则注为失效状态
                admin.disableTable(al_TableName);//删除表
                admin.deleteTable(al_TableName);
                System.out.println("删除成功");
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

8. 测试代码

运行：  
![](https://i.loli.net/2018/11/30/5c00c27053dcc.jpg)

查看Console：  
![](https://i.loli.net/2018/11/30/5c00c2bf30e83.jpg)
测试成功！

访问网址：http://202.204.62.145:16010/  
也可以看到出现了名字是'employees'的表
![](https://i.loli.net/2018/11/30/5c00c311be0f3.jpg)