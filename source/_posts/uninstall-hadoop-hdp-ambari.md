---
title: 在centos7上卸载 hadoop hdp with ambari （废弃，删除不干净，禁用）
date: 2018-3-13 17:11:28
author: sssnto
tags: Ambari hadoop 
category: 大数据
---

## hadoop hdp ambari 卸载



#### 1，通过ambari将集群中的所用组件都关闭，如果关闭不了，直接kill -9 XXX
#### 2，关闭ambari-server，ambari-agent
````
ambari-server stop  
ambari-agent stop  
````

3，卸载安装的软件
```sh
yum remove -y hadoop_2* hdp-select* ranger_2* zookeeper* bigtop* atlas-metadata* ambari* postgresql spark*  snappy*
```

以上命令可能不全，执行完一下命令后，再执行

```sh
yum list | grep @HDP  

```
查看是否还有没有卸载的，如果有，继续通过#yum remove XXX卸载

4，删除postgresql的数据

postgresql软件卸载后，其数据还保留在硬盘中，需要把这部分数据删除掉，如果不删除掉，重新安装ambari-server后，有可能还应用以前的安装数据，而这些数据时错误数据，所以需要删除掉。

```aidl
rm -rf /var/lib/pgsql  
```

5，删除用户
ambari安装hadoop集群会创建一些用户，清除集群时有必要清除这些用户，并删除对应的文件夹。这样做可以避免集群运行时出现的文件访问权限错误的问题。     

```
userdel oozie  
userdel hive  
userdel ambari-qa  
userdel flume    
userdel hdfs    
userdel knox    
userdel storm    
userdel mapred  
userdel hbase    
userdel tez    
userdel zookeeper  
userdel kafka    
userdel falcon  
userdel sqoop    
userdel yarn    
userdel hcat  
userdel atlas  
userdel spark  
```

```
rm -rf /home/atlas  
rm -rf /home/accumulo  
rm -rf /home/hbase  
rm -rf /home/hive  
rm -rf /home/oozie  
rm -rf /home/storm  
rm -rf /home/yarn  
rm -rf /home/ambari-qa  
rm -rf /home/falcon  
rm -rf /home/hcat  
rm -rf /home/kafka  
rm -rf /home/mahout  
rm -rf /home/spark  
rm -rf /home/tez  
rm -rf /home/zookeeper  
rm -rf /home/flume  
rm -rf /home/hdfs  
rm -rf /home/knox  
rm -rf /home/mapred  
rm -rf /home/sqoop  
```


6，删除ambari遗留数据

```
rm -rf /var/lib/ambari*   
rm -rf /usr/lib/python2.6/site-packages/ambari_*  
rm -rf /usr/lib/python2.6/site-packages/resource_management  
rm -rf /usr/lib/ambari-*  
rm -rf /etc/ambari-*   
```
7，删除其他hadoop组件遗留数据

```aidl
rm -rf /etc/hadoop  
rm -rf /etc/hbase  
rm -rf /etc/hive   
rm -rf /etc/oozie  
rm -rf /etc/sqoop   
rm -rf /etc/zookeeper  
rm -rf /etc/flume   
rm -rf /etc/storm   
rm -rf /etc/hive-hcatalog  
rm -rf /etc/tez   
rm -rf /etc/falcon   
rm -rf /etc/knox   
rm -rf /etc/hive-webhcat  
rm -rf /etc/kafka   
rm -rf /etc/slider   
rm -rf /etc/storm-slider-client  
rm -rf /etc/spark   
rm -rf /var/run/spark  
rm -rf /var/run/hadoop  
rm -rf /var/run/hbase  
rm -rf /var/run/zookeeper  
rm -rf /var/run/flume  
rm -rf /var/run/storm  
rm -rf /var/run/webhcat  
rm -rf /var/run/hadoop-yarn  
rm -rf /var/run/hadoop-mapreduce  
rm -rf /var/run/kafka  
rm -rf /var/log/hadoop  
rm -rf /var/log/hbase  
rm -rf /var/log/flume  
rm -rf /var/log/storm  
rm -rf /var/log/hadoop-yarn  
rm -rf /var/log/hadoop-mapreduce  
rm -rf /var/log/knox   
rm -rf /usr/lib/flume  
rm -rf /usr/lib/storm  
rm -rf /var/lib/hive   
rm -rf /var/lib/oozie  
rm -rf /var/lib/flume  
rm -rf /var/lib/hadoop-hdfs  
rm -rf /var/lib/knox   
rm -rf /var/log/hive   
rm -rf /var/log/oozie  
rm -rf /var/log/zookeeper  
rm -rf /var/log/falcon  
rm -rf /var/log/webhcat  
rm -rf /var/log/spark  
rm -rf /var/tmp/oozie  
rm -rf /tmp/ambari-qa  
rm -rf /var/hadoop  
rm -rf /hadoop/falcon  
rm -rf /tmp/hadoop   
rm -rf /tmp/hadoop-hdfs  
rm -rf /usr/hdp  
rm -rf /usr/hadoop  
rm -rf /opt/hadoop  
rm -rf /tmp/hadoop  
rm -rf /var/hadoop  
rm -rf /hadoop  

```
8，清理yum数据源

```aidl
yum clean all  
```

通过以上清理后，重新安装ambari和hadoop集群（包括HDFS，YARN+MapReduce2，Zookeeper，Ambari Metrics，Spark）成功。如果安装其他组件碰到由于未清理彻底而导致的问题，请留言指出需要清理的数据，本人会补全该文档。
