---
title: ambari hive udf 使用
date: 2018-3-13 17:11:28
author: sssnto
tags: hive hadoop  udf
category: 大数据
---

创建一个简单的udf

```
package com.jdjr.gudle.bigdata.udf;

import org.apache.hadoop.hive.ql.exec.UDF;
import org.apache.hadoop.io.Text;

/**
 * User: songpeng6
 * Date: 2018-03-22
 * Time: 18:57
 */
public class SimpleUDFExample extends UDF {

    public Text evaluate(Text input) {
        return new Text("Hello " + input.toString());
    }

}
```

```
##将jar加入classpath
ADD JAR hdfs:///user/admin/jars/gudle-bigdata-udf-0.1.0-SNAPSHOT.jar;

CREATE FUNCTION dict as 'com.jdjr.gudle.bigdata.udf.DictGenericUDF' USING JAR 'hdfs:///user/admin/jars/gudle-bigdata-udf-0.1.0-SNAPSHOT.jar';

CREATE TEMPORARY FUNCTION dict as 'com.jdjr.gudle.bigdata.udf.DictUdf';  

DROP TEMPORARY FUNCTION IF EXIST hello;

```