---
title: docker系列（一）：docker安装与简单使用
date: 2017-12-10 18:55:15
categories: 
    - docker
tags: 
    - docker命令
    - docker安装
---

本文作为docker 系列的第一篇，主要讲述docker在mac和windows下的安装与使用，基本上都是直接参考的官方文档。

<!-- more -->

## 下载桌面版docker

1. mac去这个链接下载：[[docker mac版下载](https://store.docker.com/editions/community/docker-ce-desktop-mac)]
2. windows去这个链接下载：[[docker windows版下载链接](https://store.docker.com/editions/community/docker-ce-desktop-windows)]
3. 服务器端这里不再赘述，因为docker针对每个服务器版本都会有不同的安装包，具体方式请参考官方文档，笔者的服务器端生产环境是centos7.4

## 安装
1. mac下的解压后直接拷贝到应用程序下即可使用
2. windows双击安装应用程序后即可使用

## 检查安装环境是否正常
打开命令窗口，运行以下命令，输出版本号即说明安装完成
```bash
docker -v
```

## 运行hello-world
命令行下执行以下命令：
```bash
docker run hello-word
```
看见以下输出后说明正常运行
```bash
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

好了，本篇文章内容就到这里，下篇将向大家介绍docker 镜像的基本操作指令

## 参考文档

1. [[docker中文指南](http://www.widuu.com/chinese_docker/index.html)]
