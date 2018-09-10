---
title: 离线yum本地安装
date: 2018-3-25 10:10:05
author: sssnto
tags: [yum,centos]
category: 云平台
---

1 . 先在可以下载包的机器上下载好安装包
```
yum -y install --downloadonly --downloaddir=k8s kubelet kubeadm kubectl
```

2 。 将以上 rpm 包拷贝到需要安装 kubeadm 的机器，执行setenforce 0和yum -y localinstall *.rpm即完成安装
```
setenforce 0

yum -y localinstall *.rpm
```
