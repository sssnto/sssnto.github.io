---
title: Github搭建个人博客
date: 2017-05-10 09:16:06
author: TinyKing
tags: hexo
category: 工具
---

[toc]

> github pages个人博客，使用hexo编写，使用Travis CI持续集成，自动发布

# Github Pages设置

## 什么是Github Pages
[GitHub Pages](https://pages.github.com/) 本用于介绍托管在GitHub的项目，不过，由于他的空间免费稳定，用来做搭建一个博客再好不过了。

每个帐号只能有一个仓库来存放个人主页，而且仓库的名字必须是username/username.github.io，这是特殊的命名约定。你可以通过[http://username.github.io](http://username.github.io/) 来访问你的个人主页。

## 创建自己的Github Pages
>  我的github pages就是: [http://tinyking.github.io](http://tinyking.github.io)

注册GitHub及使用Github Pages的过程已经有很多文章讲过，在此不再详述，可以参考：
[一步步在GitHub上创建博客主页 全系列](http://pchou.info/web-build/2013/01/03/build-github-blog-page-01.html)

[如何搭建一个独立博客——简明Github Pages与Hexo教程](http://www.jianshu.com/p/05289a4bc8b2)

在这里我创建了一个github repo叫做  [http://tinyking.github.io](http://tinyking.github.io). 创建完成之后，需要有一次提交(git commit)操作，然后就可以通过链接 [http://tinyking.github.io](http://tinyking.github.io) 访问了。

# hexo 环境配置

## 本地配置
网上有很多在本地配置环境的教程，请自行搜索。

## 使用模版

访问[https://github.com/tinyking/tinyking.github.io](https://github.com/tinyking/tinyking.github.io)，切换到hexo分支

![tinyking.github.io](http://upload-images.jianshu.io/upload_images/872613-4beb458855d3a24f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

选择下载

![image.png](http://upload-images.jianshu.io/upload_images/872613-61dd77bedc476f85.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果本地没有node、hexo环境，是无法运行，查看hexo博客的。不过可以通过Travis CI集成。

# Travis CI集成

## 1.修改git地址

![tinyking.github.io](http://upload-images.jianshu.io/upload_images/872613-939f6f1677ac4e79.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

进入.git， 打开config


![tinyking.github.io](http://upload-images.jianshu.io/upload_images/872613-9a5750002c7868d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![config文件](http://upload-images.jianshu.io/upload_images/872613-6852814908eb6708.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将url修改成你自己的github地址

## 2. 修改`.travis.xml`

![.travis.xml](http://upload-images.jianshu.io/upload_images/872613-fdd8ff3c5e13470d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

修改成自己的用户名和邮箱

## 3.创建github token
> 注意：需要复制该token稍后使用，token值只能看到一次。

![tinyking.github.io](http://upload-images.jianshu.io/upload_images/872613-61a3f1fd2aa95616.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 4. 创建Travis任务

![tinyking.github.io](http://upload-images.jianshu.io/upload_images/872613-452afd0057149416.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将Github的token添加到目标位置

![tinyking.github.io](http://upload-images.jianshu.io/upload_images/872613-6760365131298d98.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 5. 上传github
将博客push到github即可
