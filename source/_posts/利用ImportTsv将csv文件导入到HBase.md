---
title: 利用ImportTsv将csv文件导入到HBase
date: 2019-11-30 10:47:42
tags:
  - BigData（大数据）
  - HBase
categories:
  - [BigData（大数据）, HBase]
---
参考网址：
> https://blog.csdn.net/scgaliguodong123_/article/details/46771929

---

#### 编写simple.csv文件
`cd ~`  
`vi simple.csv`  
加入
```
0,"Tony"
1,"Ivy"
2,"Tom"
3,"Spark"
4,"Storm"
```
保存
<!-- more -->
#### 把csv文件数据导入HDFS
`hdfs dfs -mkdir /user`

`hdfs dfs -mkdir /user/hadoop`

`hdfs dfs -put /home/hadoop/simple.csv /user/hadoop`

查看`hdfs dfs -ls /user/hadoop`

#### 创建表
进入hbase shell`hbase shell`

创建hbase-tb1-001表`create 'hbase-tb1-001','cf'`

#### 使用Map Reduce导入数据到HBase
`hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.separator="," -Dimporttsv.columns=HBASE_ROW_KEY,cf hbase-tb1-001 /user/hadoop/simple.csv`

####  查看结果
进入hbase shell`hbase shell`

`scan 'hbase-tb1-001'`

成功截图：
![](https://i.loli.net/2018/11/30/5c0122e19babc.jpg)

#### 删除测试用目录
`hdfs dfs -rm -r /user/hadoop`

---
### 多列情况

#### 编写simple2.csv文件
`cd ~`  
`vi simple2.csv`  
加入
```
0,"Tony","a"
1,"Ivy","b"
2,"Tom","c"
3,"Spark","d"
4,"Storm","e"
```
保存

#### 把csv文件数据导入HDFS
`hdfs dfs -mkdir /user`

`hdfs dfs -mkdir /user/hadoop`

`hdfs dfs -put /home/hadoop/simple2.csv /user/hadoop`

查看`hdfs dfs -ls /user/hadoop`

#### 创建表
进入hbase shell`hbase shell`

创建hbase-tb1-001表`create 'hbase-tb1-002','cf','cg'`

#### 使用Map Reduce导入数据到HBase
`hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.separator="," -Dimporttsv.columns=HBASE_ROW_KEY,cf,cg hbase-tb1-002 /user/hadoop/simple2.csv`

####  查看结果
进入hbase shell`hbase shell`

`scan 'hbase-tb1-002'`

成功截图：
![](https://i.loli.net/2018/11/30/5c0124bebb97b.jpg)

#### 删除测试用目录
`hdfs dfs -rm -r /user/hadoop`

---
### 多列且使用列限定符，即列(columnFamily:columnQualifier)的情况

*除了下两个步骤外，其余步骤与上面一样*

#### 创建表
进入hbase shell`hbase shell`

创建hbase-tb1-001表`create 'hbase-tb1-003','cf'`

#### 使用Map Reduce导入数据到HBase
只要改变Map Reduce的命令即可：

`hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.separator="," -Dimporttsv.columns=HBASE_ROW_KEY,cf:cq1,cf:cq2 hbase-tb1-003 /user/hadoop/simple2.csv`

---

#### 导入`res_0915-0922_copy.csv`文件命令

`hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.separator="," -Dimporttsv.columns=HBASE_ROW_KEY,cf:cqTimestamp,cf:cq1,cf:cq2,cf:cq3,cf:cq4,cf:cq5,cf:cq7,cf:cq8,cf:cq9,cf:cq10,cf:cq11,cf:cq12,cf:cq14,cf:cq15,cf:cq16,cf:cq17,cf:cq18,cf:cq20,cf:cq21,cf:cq22,cf:cq24,cf:cq25,cf:cq27,cf:cq28,cf:cq29,cf:cq30,cf:cq31,cf:cq32,cf:cq33,cf:cq34,cf:cq47,cf:cq49,cf:cq50,cf:cq52,cf:cq53,cf:cq54,cf:cq56,cf:cq57 TestData /user/hadoop/res_0915-0922_copy.csv`



![](https://i.loli.net/2018/12/08/5c0b23a0ba999.jpg)


+ z
+ a
+ z
+ 



1. asjkl
1. jsdaf
1. dasfj
1. asdfjlk
2. 


`conn = DriverManager.getConnection(url, user, password);`

```
		public Connection getConnection() {
			try {
				Class.forName(className);
				conn = DriverManager.getConnection(url, user, password);
			} catch (ClassNotFoundException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			} catch (SQLException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			return conn;
		}
```