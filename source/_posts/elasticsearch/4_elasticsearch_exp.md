---
layout: "post"
title: "elasticsearch 学习笔记4-简单的小需求"
date: "2018-09-18 13:55"
tags: [elasticsearch,'分布式搜索']
category: [elasticsearch]
comments: true    // 是否开启评论
---

# 适应elasticsearch

之前对elasticsearch的安装和简单的测试已经进行完了， 下面我们要通过一个简单小需求来进行elasticsearch探索之路

# 场景
我们受雇于 Megacorp 公司，作为 HR 部门新的 “热爱无人机” （_"We love our drones!"_）激励项目的一部分，我们的任务是为此创建一个雇员目录。该目录应当能培养雇员认同感及支持实时、高效、动态协作，因此有一些业务需求：

- 支持包含多值标签、数值、以及全文本的数据
- 检索任一雇员的完整信息
- 允许结构化搜索，比如查询 30 岁以上的员工
- 允许简单的全文搜索以及较复杂的短语搜索
- 支持在匹配文档内容中高亮显示搜索片段
- 支持基于数据创建和管理分析仪表盘
