---
title: NFCA-编写Dockerfile，自动执行脚本
date: 2021-04-15 14:58:51
tags:
  - Docker
  - Dockerfile
  - NFCA
categories:
  - [Docker, Dockerfile]
  - [NFCA]
---
# 分步进行
1. `cd nfcadb`
2. `tar -czvf nfcadb.tar.gz *`
3. 编写buffer_to_mongodb.sh
```
<!--cd /opt/nfcadb/-->
<!--tar -zxvf nfcadb.tar.gz-->
<!--cd /opt/nfcadb/buffer_to_nfcadb-->
<!--python3 data_import.py --mode nfca-->

<!--20210522更新-->
cd /opt/nfcadb/buffer_to_nfcadb
python3 data_import.py --mode nfca
```

<!-- more -->

4. 编写Dokcerfile
```
<!--FROM python:3.6-slim-buster-->
<!--COPY ./nfcadb.tar.gz /opt/nfcadb/-->
<!--COPY ./buffer_to_mongodb.sh /opt/-->
<!--RUN pip3 install --upgrade pip \-->
<!--    && pip3 install numpy pymysql pyyaml pymongo influxdb_client-->

<!--CMD ["sh", "/opt/buffer_to_mongodb.sh"]-->

<!--20210522更新-->
FROM python:3.6-slim-buster
COPY ./nfcadb.tar.gz /opt/nfcadb/
COPY ./buffer_to_mongodb.sh /opt/
RUN pip3 install --upgrade pip \
    && pip3 install numpy pymysql pyyaml pymongo influxdb_client

CMD ["sh", "/opt/nfcadb/buffer_to_mongodb.sh"]
```
5. `docker build -t buffer_to_mongodb:v0 .`
6. `docker run -itd --name buff_to_mongo buffer_to_mongodb:v0`
7. 查看实时docker容器日志`docker logs -f buff_to_mongo`
8. 访问网站`http://159.138.163.117:16005`，可视化查看容器日志

# 编写sh脚本
1. 编写脚本：
```
docker stop buff_to_mongo
docker rm buff_to_mongo
docker build -t buffer_to_mongodb:v0 .
docker run -itd --name buff_to_mongo -v /home/nfca-admin/nfcadb/:/opt/nfcadb/ buffer_to_mongodb:v0
```
2. `sudo sh ~/nfcadb/update_buff_to_mongo_image.sh`

3. 查看实时docker容器日志`docker logs -f buff_to_mongo`
4. 访问网站`http://159.138.163.117:16005`，可视化查看容器日志