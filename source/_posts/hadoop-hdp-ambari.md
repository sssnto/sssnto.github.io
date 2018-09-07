---
title: 在centos7上安装hadoop hdp with ambari
date: 2018-3-13 17:11:28
author: sssnto
tags: Ambari hadoop 
category: 大数据
---

## hadoop hdp ambari 安装

### 一. 安装环境描述

 - 操作系统： centos 7
 
机器名称 | ip 
- | :-: 
instance-1 | 10.140.0.2
instance-2 | 10.140.0.3
instance-3 | 10.140.0.4


### 二. 安装前准备
#### 1. 机器免密码登录
1. 切换到root用户下执行ssh-keygen,一直回车，会产生公钥文件在~/.ssh中
```
[root@instance-1 .ssh]# ssh-keygen
[root@instance-1 .ssh]# ll
total 16
-rw-------. 1 root root  795 Mar 13 08:29 authorized_keys
-rw-------. 1 root root 1675 Mar 13 08:25 id_rsa
-rw-r--r--. 1 root root  397 Mar 13 08:25 id_rsa.pub
```
2.将SSH公钥添加到目标主机上的authorized_keys文件中。
```
cat id_rsa.pub >> authorized_keys
```
目标主机就是除了当前机器的其他两台机器
3.设置.ssh目录权限为700 ， 设置authorized_keys文件权限为600
```
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```
4.尝试ssh连接其他机器
```
ssh instance-2
```
5.如果遇到连接异常，修改sshd_config配置文件
```
vim /etc/ssh/sshd_config
```
将里面的PermitRootLogin改为yes
```
PermitRootLogin yes
```
保存后，重启sshd服务
```
service sshd restart
```
在三台机器都需要做免密码登录操作

----------
#### 2. 主机上启用NTP，时间服务器
```
yum install -y ntp
systemctl enable ntpd
```
----------
#### 3. 关闭防火墙
```
systemctl disable firewalld
service firewalld stop
```
#### 4. 禁用SELinux和PackageKit将检查umask值
禁用SELinux
```
setenforce 0
```
要永久禁用SELinux set SELINUX = disabled in /etc/selinux/config这将确保SELinux在重启机器后不会自行打开。
```
[root@instance-1 ~]# vi /etc/selinux/config

# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=disabled 
# SELINUXTYPE= can take one of three two values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted

```
设置UMASK为0022
检查umask的值，如果为0022则不需要操作
```
[root@instance-1 ~]# umask
0022
```
如果umask不为0022
永久更改所有交互式用户的umask：
```
echo umask 0022 >> /etc/profile
```
### 3.安装Ambari
#### 1. 下载Ambari存储库
 1. 以root用户登录
 2. 将Ambari存储库文件下载到安装主机上的目录，没有wget工具可先安装wget
 ```
 [root@instance-1 ~]# yum install -y wget
 wget -nv http://public-repo-1.hortonworks.com/ambari/centos7/2.x/updates/2.6.1.0/ambari.repo -O /etc/yum.repos.d/ambari.repo
 ```
>[重要]	重要
不要修改ambari.repo文件名称。在代理注册期间，该文件预计在Ambari服务器主机上可用。
3. 查看repo是否添加成功  yum repolist
```
[root@instance-1 ~]# yum repolist
Loaded plugins: fastestmirror
ambari-2.6.1.0                       | 2.9 kB     00:00     
ambari-2.6.1.0/primary_db              | 8.6 kB   00:00     
Loading mirror speeds from cached hostfile
 * base: centos.sonn.com
 * epel: mirrors.cat.pdx.edu
 * extras: repos-lax.psychz.net
 * updates: centos.mirror.ndchost.com
repo id              repo name                        status
ambari-2.6.1.0       ambari Version - ambari-2.6.1.0      12
base/7/x86_64        CentOS-7 - Base                   9,591
epel/x86_64          Extra Packages for Enterprise Li 12,400
extras/7/x86_64      CentOS-7 - Extras                   435
google-cloud-compute Google Cloud Compute                  9
google-cloud-sdk     Google Cloud SDK                    413
updates/7/x86_64     CentOS-7 - Updates                2,404
repolist: 25,264
```
#### 2. 安装Ambari服务器
1. 安装Ambari。这也安装默认的PostgreSQL Ambari数据库。
```
[root@instance-1 ~]# yum install -y ambari-server
......
Transaction test succeeded
Running transaction
  Installing : postgresql-libs-9.2.23-3.el7_4.x86_64               1/4 
  Installing : postgresql-9.2.23-3.el7_4.x86_64                    2/4 
  Installing : postgresql-server-9.2.23-3.el7_4.x86_64             3/4 
  Installing : ambari-server-2.6.1.0-143.x86_64                    4/4 
  Verifying  : postgresql-9.2.23-3.el7_4.x86_64                    1/4 
  Verifying  : ambari-server-2.6.1.0-143.x86_64                    2/4 
  Verifying  : postgresql-server-9.2.23-3.el7_4.x86_64             3/4 
  Verifying  : postgresql-libs-9.2.23-3.el7_4.x86_64               4/4 
Installed:
  ambari-server.x86_64 0:2.6.1.0-143                                                              
Dependency Installed:
  postgresql.x86_64 0:9.2.23-3.el7_4   postgresql-libs.x86_64 0:9.2.23-3.el7_4   postgresql-server.x86_64 0:9.2.23-3.el7_4  
Complete!
```
#### 3. 设置服务器

三台机器都需安装java jdk，设置环境变量
```
vim /etc/profile

export JAVA_HOME=/export/servers/jdk1.8.0_161
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

export HADOOP_HOME=/usr/hdp/2.6.4.0-91/hadoop
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=/usr/hdp/2.6.4.0-91/hadoop-hdfs
export HADOOP_YARN_HOME=/usr/hdp/2.6.4.0-91/hadoop-yarn
export HADOOP_MAPRED_HOME=/usr/hdp/2.6.4.0-91/hadoop-mapreduce
export HADOOP_CONF_DIR=/usr/hdp/2.6.4.0-91/hadoop/conf
export HADOOP_USER_NAME=gudle
export TZ="Asia/Shanghai"


source /etc/profile
```


上传myslq jdbc驱动包。 稍后启动时指定mysql驱动包
在装有mysql的机器上初始化数据
scp /var/lib/ambari-server/resources/Ambari-DDL-MySQL-CREATE.sql instance-2:~

```
[root@instance-2 ~]# mysql -uroot -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 32
Server version: 5.5.56-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> use ambari;
Database changed
MariaDB [ambari]> 
MariaDB [ambari]> 
MariaDB [ambari]> source /root/Ambari-DDL-MySQL-CREATE.sql
Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.01 sec)

Query OK, 0 rows affected (0.00 sec)
```

cp驱动包
```
mkdir /usr/share/java
cp ./jdbc/mysql-connector-java-5.1.46/mysql-connector-java-5.1.46.jar /usr/share/java/

vim /etc/ambari-server/conf/ambari.properties

server.jdbc.driver.path=/usr/share/java/mysql-connector-java-5.1.46.jar


```

设置jdbc路径
```
ambari-server setup --jdbc-db=mysql --jdbc-driver=/usr/share/java/mysql-connector-java-5.1.46.jar
```
ambari-server 服务器设置
```
[root@instance-1 ~]# ambari-server setup
Using python  /usr/bin/python
Setup ambari-server
Checking SELinux...
SELinux status is 'enabled'
SELinux mode is 'permissive'
WARNING: SELinux is set to 'permissive' mode and temporarily disabled.
OK to continue [y/n] (y)? y
Customize user account for ambari-server daemon [y/n] (n)? y
Enter user account for ambari-server daemon (root):
Adjusting ambari-server permissions and ownership...
Checking firewall status...
Checking JDK...
[1] Oracle JDK 1.8 + Java Cryptography Extension (JCE) Policy Files 8
[2] Oracle JDK 1.7 + Java Cryptography Extension (JCE) Policy Files 7
[3] Custom JDK
==============================================================================
Enter choice (1): 1
To download the Oracle JDK and the Java Cryptography Extension (JCE) Policy Files you must accept the license terms found at http://www.oracle.com/technetwork/java/javase/terms/license/index.html and not accepting will cancel the Ambari Server setup and you must install the JDK and JCE files manually.
Do you accept the Oracle Binary Code License Agreement [y/n] (y)? y
Downloading JDK from http://public-repo-1.hortonworks.com/ARTIFACTS/jdk-8u112-linux-x64.tar.gz to /var/lib/ambari-server/resources/jdk-8u112-linux-x64.tar.gz
jdk-8u112-linux-x64.tar.gz... 100% (174.7 MB of 174.7 MB)
Successfully downloaded JDK distribution to /var/lib/ambari-server/resources/jdk-8u112-linux-x64.tar.gz
Installing JDK to /usr/jdk64/
Successfully installed JDK to /usr/jdk64/
Downloading JCE Policy archive from http://public-repo-1.hortonworks.com/ARTIFACTS/jce_policy-8.zip to /var/lib/ambari-server/resources/jce_policy-8.zip

Successfully downloaded JCE Policy archive to /var/lib/ambari-server/resources/jce_policy-8.zip
Installing JCE policy...
Checking GPL software agreement...
GPL License for LZO: https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html
Enable Ambari Server to download and install GPL Licensed LZO packages [y/n] (n)? y
Completing setup...
Configuring database...
Enter advanced database configuration [y/n] (n)? 
Configuring database...
Default properties detected. Using built-in database.
Configuring ambari database...
Checking PostgreSQL...
Running initdb: This may take up to a minute.
Initializing database ... OK


About to start PostgreSQL
Configuring local database...
Configuring PostgreSQL...
Restarting PostgreSQL
Creating schema and user...
done.
Creating tables...
done.
Extracting system views...
ambari-admin-2.6.1.0.143.jar
...........
Adjusting ambari-server permissions and ownership...
Ambari Server 'setup' completed successfully.
```
#### 4. 安装，配置和部署群集
1. 启动Ambari服务器
```
ambari-server start
```
要检查Ambari服务器进程
```
ambari-server status
```
要停止Ambari服务器
```
ambari-server stop
```
在Ambari服务器启动时，Ambari运行数据库一致性检查以查找问题。如果发现任何问题，Ambari服务器启动将中止并显示以下消息：DB configs consistency check failed。Ambari将更多关于数据库一致性检查结果的细节写入/var/log/ambari-server/ambari-server-check-database.log 文件。

您可以使用以下选项跳过此检查来启动Ambari Server：
```
ambari-server start --skip-database-check
```

在hdfs上创建admin用户目录
```
sudo su - hdfs
hdfs dfs -mkdir /user/admin
hdfs dfs -chown admin:hadoop /user/admin
```

修改hdfs参数
HDFS->Configs->Advanced->customer core-site
```
hadoop.proxyuser.hcat.hosts=*
hadoop.proxyuser.hive.hosts=*
hadoop.proxyuser.root.hosts=*
```
YARN->configs->Advanced->Custom yarn-site
```
yarn.timeline-service.http-authentication.proxyuser.root.hosts
```

安装ngxin，如果可以直接用8080访问，不用配置nginx
```
yum install -y nginx
```
配置ngxin,将8080端口映射到ambari.sssnto.cn域名下
```
vim /etc/nginx/conf.d/ambari.conf

server {
    listen 80;
    server_name ambari.sssnto.cn;
    location / {
        proxy_pass http://127.0.0.1:8080;
    }
}
```
ngxin刷新配置
```
service nginx start
 nginx -s reload
```

 - 登录到Apache Ambari
先决条件
 - Ambari服务器必须正在运行\
 - 使用Web浏览器登录Ambari Web
HTTP：// <your.ambari.server>：8080
默认用户名密码 admin/admin

启动Ambari集群安装向导
从Ambari欢迎页面选择启动安装向导。
![](http://ww1.sinaimg.cn/large/bc44ba23ly1fpcgnxrfldj20z80k4q4j.jpg)
点击启动安装向导
填写集群名称，点击next
![](http://ww1.sinaimg.cn/large/bc44ba23ly1fpcgqen3i7j210q0f7t9s.jpg)

选择版本
![](http://ww1.sinaimg.cn/large/bc44ba23ly1fpd9smvo37j21hc1rxtga.jpg)

填写主机名称
确保hostname的配置符合Fully Qualified Domain Name (FQDN)规则，即hostname.domainname，
私钥填写安装ambari的私钥
![](http://ww1.sinaimg.cn/large/bc44ba23ly1fpd9t94y6lj21100kwwgk.jpg)
点击下一步
![](http://ww1.sinaimg.cn/large/bc44ba23ly1fpd9umh6eej211h0h6mza.jpg)
选择服务
![](http://ww1.sinaimg.cn/large/bc44ba23ly1fpd9ymrnu5j21hc1ign3s.jpg)
选择master服务
![](http://ww1.sinaimg.cn/large/bc44ba23ly1fpda0n0d7tj21hc1uy46q.jpg)
选择slave
![](http://ww1.sinaimg.cn/large/bc44ba23ly1fpda2hluf8j21hc0p0acp.jpg)
设置参数
![](http://ww1.sinaimg.cn/large/bc44ba23ly1fpda4c5pqgj21hc14fjvo.jpg)
未完待续。。。。。

mysql安装失败，执行下面语句
```
rpm -Uvh http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
```

```
[root@instance-1 ~]# vim /etc/ambari-server/conf/ambari.properties

## 报超过50s错误，可以加这个属性解决
server.startup.web.timeout=120
```



解决hive中文乱码问题
修改字段注释字符集
```
alter table COLUMNS modify column COMMENT varchar(256) character set utf8;
```
修改表注释字符集
```
alter table TABL_PARAMS modify column PARAM_VALUE varchar(4000) character set utf8;
```

修改表分区注释字符集
```
alter table PARTITION_KEYS modify column PKEY_COMMENT varchar(4000) character set utf8;
```

最后别忘记修改hive的jdbc连接，将字符集修改为utf8，如
```
javax.jdo.option.ConnectionURL
jdbc:mysql://192.168.0.128:3306/hive?characterEncoding=UTF-8
```  

