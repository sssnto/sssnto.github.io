---
layout: "post"
title: "elasticsearch 学习笔记1-课程准备以及学习安装"
date: "2018-09-18 11:02"
tags: [elasticsearch,'分布式搜索']
category: [elasticsearch]
comments: true    // 是否开启评论
---

# 1. 安装并运行 Elasticsearch

运行环境

- [jdk](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
- [Elasticsearch安装包](elastic.co/downloads/elasticsearch)
 - [kibana](https://www.elastic.co/downloads/kibana)
 
## 1. java 安装。 官网下载后正常安装。不做赘述。
## 2. elasticsearch安装
要想安装 Elasticsearch，先下载并解压适合你操作系统的 Elasticsearch 版本。如果你想了解更多的信息， 可以查看 Elasticsearch 参考手册里边的安装部分，这边给出的链接指向安装说明 [Installation](https://www.elastic.co/guide/en/elasticsearch/reference/master/_installation.html)。

下载文件并解压，并且执行启动命令

```sh
cd elasticsearch-<version>
./bin/elasticsearch
```

测试 Elasticsearch 是否启动成功，可以打开另一个终端，执行以下操作：

```
curl 'http://localhost:9200/?pretty'

```

上面是官网给出的测试方法， 本地测试用浏览器直接访问地址即可
请求上面的地址可返回一下数据

```json
{
  "name" : "Tom Foster",
  "cluster_name" : "elasticsearch",
  "version" : {
    "number" : "2.1.0",
    "build_hash" : "72cd1f1a3eee09505e036106146dc1949dc5dc87",
    "build_timestamp" : "2015-11-18T22:40:03Z",
    "build_snapshot" : false,
    "lucene_version" : "5.3.1"
  },
  "tagline" : "You Know, for Search"
}
```

能返回这个信息就说明你已经成功启动一个Elasticsearch 节点了。你可以用它做实验了。
单个 节点 可以作为一个运行中的 Elasticsearch 的实例。 而一个 集群 是一组拥有相同 cluster.name 的节点， 他们能一起工作并共享数据，还提供容错与可伸缩性。(当然，一个单独的节点也可以组成一个集群) 你可以在 elasticsearch.yml 配置文件中 修改 cluster.name ，该文件会在节点启动时加载 (译者注：这个重启服务后才会生效)。 关于上面的 cluster.name 以及其它 Important Configuration Changes 信息， 你可以在这本书后面提供的生产部署章节找到更多。

## 3.  安装Kibana
 Kibana 应用 它提供交互式的控制台，通过你的浏览器直接向 Elasticsearch 提交请求。

 - [kibana](https://www.elastic.co/downloads/kibana)

1. 下载kibana并且解压，启动kibana

```sh
./bin/kibana
```

> windows 版本启动kibana： ./bin/kibana.bat

2. 用浏览器打开应用：http://localhost:5601/app/kibana 显示以下界面为安装成功
![Screenshot](https://i.imgur.com/HJHgbLW.png)
