---
title: Hexo环境安装配置
date: 2017-12-09 20:46:25
categories: [hexo相关]
tags: [hexo命令行]
---

本文主要介绍node.js的安装，npm的安装，hexo环境配置及相关命令使用

<!-- more -->

## 安装nodejs

1. [[node.js下载链接](https://nodejs.org/en/download/)]

2. 安装完成后检查环境是否正常


``` bash
$ node --version
v8.9.2  //出现这个版本号说明已完成安装
        //npm已经包含在新版本的node中，不需要再安装
```

## hexo安装

```bash
//创建博客根目录
$ mkdir blog
$ cd blog
// 安装hexo
$ npm install hexo -g
//升级hexo
$ npm update hexo -g
//初始化目录
$ hexo init
//启动服务
$ hexo server
```

## hexo相关命令行

### 简写

```bash
hexo n "我的博客" == hexo new "我的博客" #新建文章
hexo p == hexo publish
hexo g == hexo generate#生成
hexo s == hexo server #启动服务预览
hexo d == hexo deploy#部署
```

### 服务器

```bash
hexo server #Hexo 会监视文件变动并自动更新，您无须重启服务器。
hexo server -s #静态模式
hexo server -p 5000 #更改端口
hexo server -i 192.168.1.1 #自定义 IP

hexo clean #清除缓存 网页正常情况下可以忽略此条命令
hexo g #生成静态网页
hexo d #开始部署
```

### 监视文件变动

```bash
hexo generate #使用 Hexo 生成静态文件快速而且简单
hexo generate --watch #监视文件变动
```

### 页面相关

```bash
hexo new "postName"         #新建文章
hexo new page "pageName"    #新建页面
hexo new page tags          #新建标签页，刚刚初始化的博客目录需要使用此命令新建标签页
hexo new page categories    #新建分类页，刚刚初始化的根目录需要使用此命令新建分类页
```

### 页面顶部主要内容

```markdown
title: 使用Hexo搭建个人博客
layout: post
date: 2014-03-03 19:07:43
comments: true
categories: Blog
tags: [Hexo]
keywords: Hexo, Blog
description: 文章描述
```

### 设置文章摘要

```markdown
以上是文章摘要 <!-- more --> 以下是余下全文
```