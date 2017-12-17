---
title: consulServer系列（一）：consul单机版安装
date: 2017-12-17 19:50:58
categories:
    - consul
tags:
    - consul
    - consulServer
---

本篇为consul系列的开篇，主要介绍consul服务端的安装与使用。照例，使用版本依然为consul截止目前为止的最新版

<!-- more -->

1. 从官方网站下载consul软件。[[官方网站下载链接](https://www.consul.io/downloads.html)]

2. 解压下载好的压缩包，并以代理模式运行。
```bash
./consul agent -server -bootstrap -data-dir /tmp/consul/ -ui
```

至此，一个单机版的consul服务端就起来了，下篇将会介绍一个集群的部署方式。
