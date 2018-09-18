---
layout: "post"
title: "elasticsearch 学习笔记2-与es之间的交互"
date: "2018-09-18 11:34"
tags: [elasticsearch,'分布式搜索']
category: [elasticsearch]
comments: true    // 是否开启评论
---

# 与es之间的交互

- java api
- 通过restful的方式

上一节我们安装了kibana工具，我们可以通过工具来与es之间的一些交互测试


## 1. 打开应用，选择到 dev tools 应用
![微信图片_20180918112836](https://i.imgur.com/MQq6wPu.png)


## 2. 测试交互功能
- 计算集群中文档的数量，我们可以用这个:

```
curl -XGET 'http://localhost:9200/_count?pretty' -d '
{
    "query": {
        "match_all": {}
    }
}
'
```

Elasticsearch 返回一个 HTTP 状态码（例如：200 OK`）和（除`HEAD`请求）一个 JSON 格式的返回值。前面的 `curl 请求将返回一个像下面一样的 JSON 体：

```JSON

{
    "count" : 0,
    "_shards" : {
        "total" : 5,
        "successful" : 5,
        "failed" : 0
    }
}

```

在返回结果中没有看到 HTTP 头信息是因为我们没有要求curl`显示它们。想要看到头信息，需要结合 `-i 参数来使用 curl 命令：

```curl
curl -i -XGET 'localhost:9200/'

```

我们将用缩写格式来展示这些 curl 示例，所谓的缩写格式就是省略请求中所有相同的部分，例如主机名、端口号以及 curl 命令本身。而不是像下面显示的那样用一个完整的请求：

```
curl -XGET 'localhost:9200/_count?pretty' -d '
{
    "query": {
        "match_all": {}
    }
}'
```

我们将用缩写格式显示：

```
GET /_count
{
    "query": {
        "match_all": {}
    }
}
```
这种写法在kibana种也是支持的。
