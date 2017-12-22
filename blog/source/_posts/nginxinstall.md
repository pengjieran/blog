---
title: nginx编译安装
date: 2017-12-19 16:16:25
categories: [nginx]
tags: [nginx安装]
---

本文主要介绍nginx在centos7版本中的源代码编译安装步骤。

<!-- more -->

## nginx编译安装

### gcc安装
```bash
yum install gcc-c++
```
### PCRE安装
```bash
yum install -y pcre pcre-devel
```
### zlib安装
```bash
yum install -y zlib zlib-devel
```
### openssl安装
```bash
yum install -y openssl openssl-devel
```
### nginx编译安装
    1. 官方下载nginx源码[[nginx下载地址](http://nginx.org/en/download.html)]
    2. 配置
(1.默认配置)<br>
```bash
./configure
```
（2.自定义配置）<br>
```bash
./configure \
--prefix=/usr/local/nginx \
--conf-path=/usr/local/nginx/conf/nginx.conf \
--pid-path=/usr/local/nginx/conf/nginx.pid \
--lock-path=/var/lock/nginx.lock \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-http_gzip_static_module \
--http-client-body-temp-path=/var/temp/nginx/client \
--http-proxy-temp-path=/var/temp/nginx/proxy \
--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
--http-scgi-temp-path=/var/temp/nginx/scgi
```

    3. 编译安装
```bash
make
make install
```
### 模块选项

|默认禁用模块|描述|
|:--|:--|
|–without-http_charset_module|禁用Charset模块，该模块用于对网页重新编码|
|–without-http_gzip_module|禁用gzip压缩模块|
|–without-http_ssi_module|禁用服务端包含模块|
|–without-http_userid_module|禁用用户ID模块，该模块为用户通过cookie验证身份|
|–without-http_access_module|禁用访问模块，对于指定的IP段，允许访问配置|
|–without-http_auth_basic_module|禁用基本的认证模块|
|–without-http_autoindex_module|禁用自动索引模块|
|–without-http_geo_module|禁用Geo模块，该模块允许你定义依赖于IP地址段的变量|
|–without-http_map_module|禁用map模块，该模块允许你定义map区段|
|–without-http_referer_module|禁用referer控制模块|
|–without-http_rewrite_module|禁用rewrite模块|
|–without-http_proxy_module|禁用代理模块，该模块用于向其他服务器传输请求|
|–without-http_fastcgi_module|禁用FastCGI模块，该模块用于和FastCGI进程配合工作|
|–without-http_memcached_module|禁用memcached模块|
|–without-http_limit_req_module|禁用Limit Request模块，该模块允许限制每个用户请求总数|
|–without-http_empty_gif_module|禁用Empty Gif模块，该模块用于在内存中提供空白GIF图像|
|–without-http_browser_module|禁用Browser模块，该模块用于解释用户代理字符串|
|–without-http_upstream_ip_hash_module|禁用Upstream模块，该模块用于配置负载均衡结构|

|默认开启模块|描述|
|:--|:--|
|–with-http_ssl_module|开启SSL模块，支持使用HTTPS协议的网页|
|–with-http_realip_module|开启Real IP支持，该模块用于从用户的请求头数据中读取real IP地址|
|–with-http_addition_module|开启Addition模块，该模块允许你追加或前置数据到响应的主体部分|
|–with-http_xslt_module|开启XLST模块，该模块实现XLST转化为XML文档|
|–with-http_image_filter_module|开启Image Filter模块，该模块允许修改图像(注意：若编译该模块，需安装libgd库)|
|–with-http_geoip_module|开启GeoIP模块，该模块通过使用MaxMind’s GeoI二进制数据库来获取客户端在地理上的分布(注意：如果希望编译该模块，需要安装libgeoip)|
|–with-http_sub_module|开启Substitution模块，该模块用于在网页中替换文本|
|–with-http_dav_module||
|–with-http_flv_module|开启FLV模块，该模块用于处理.flv(flash视频)文件|
|–with-http_gzip_static_module|开启Gzip静态模块，该模块用于发送预压缩文件|
|–with-http_random_index_module|开启Random Index模块，该模块用于挑选一个随机的文件作为该目录的index|
|–with-http_secure_link_module|开启Secure Link模块，该模块用于在URL中检测关键字的存在|
|–with-http_stub_status_module|开启Stub Status模块，该模块会产生一个服务器状态和信息页|
|–with-google_perftools_module|开启google性能工具|

|其他选项|描述|
|:--|:--|
|–with-ipv6|开启对ipv6支持|
|–without-http|禁用http服务|
|–without-http-cache|禁用http缓冲功能|
|–add-module|通过指定的路径编译添加第三方模块，如果需要编译多个模块，该选项可以多次使用|
|–with-debug|开启记录额外调试信息|

### nginx命令
```bash
./nginx -s stop #停止
./nginx -s quit #优雅退出
./nginx -s reload #重新加载配置信息
```
