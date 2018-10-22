---
title: OAuth2认证和授权原理
date: 2016-05-02 16:16:25
categories: [oauth2]
tags: [认证]
---

本篇文章主要介绍OAuth2.0版本的认证规则。每个站点的oauth认证流程都是相同的，看了本篇文章，相信你能对oauth有更深的认识
<!-- more -->

# OAuth2.0认证和授权原理解析
OAuth是一个关于授权（authorization）的开放网络标准，在全世界得到广泛应用，目前的版本是2.0版。
本文对OAuth 2.0的设计思路和运行流程，做一个简明通俗的解释，主要参考材料为<a href="http://www.rfcreader.com/#rfc6749">rfc6749</a>
## 应用场景
为了理解适用场景，假设一个场景。
有一个叫”爱阅读”的应用，想要利用QQ众多的用户群，获取QQ用户的信息，关键问题是，腾讯是不会允许这款应用直接读取用户信息的，
另外一种方案是存储用户的qq账号和密码，通过登录获取信息，但是，直接存储用户名和密码是不安全的，一旦用户修改了qq密码，这个
应用也是不知道的，从而无法获取用户信息。尊重隐私权的考虑，也不应该在用户不知情的情况下直接读取用户信息。OAuth就是为了解决上面这种场景诞生的。
## 名词定义
OAuth2.0定义了如下几个专业名词，理解这几个名词对理解后面的讲解至关重要。
* Third-party application
第三方应用，又称client,客户端，即上一节提到的那个应用
* HTTP service
http服务提供方，即上一节所说的QQ
* Resource Owner
资源所有者，即QQ用户
* User Agent
用户代理，本文中就是指浏览器。
* Authorization server
认证服务器，即服务提供商专门用来处理认证的服务器。
* Resource server
资源服务器，即服务提供商存放用户生成的资源的服务器。它与认证服务器，可以是同一台服务器，也可以是不同的服务器。
## OAuth的思路
OAuth在客户端和资源提供方之间设置了一个授权层，客户端不能直接从资源提供方获取资源，只能先访问授权层，以此将用户和客户端区分开，
客户端访问资源所使用的令牌，与用户的密码不同，用户可以在登录的时候，指定授权层令牌的权限范围和有效期。客户端在访问用户资源时，
资源层根据令牌的访问范围和有效期，开放指定资源的访问权限
## 运行流程
OAuth2.0的运行流程如下，摘自<a href="http://www.rfcreader.com/#rfc6749">rfc6749</a>
![](./images/oauth2liucheng.png)
(A) 用户打开客户端后，客户端要求用户提供授权的必要信息<br>
(B) 用户同意给予授权<br>
(C) 客户端使用上一步获得的授权，像认证服务器申请令牌<br>
(D) 认证服务器对客户端认证，确认无误，发放访问令牌<br>
(E) 客户端使用令牌，想资源服务器请求资源<br>
(F) 资源服务器确认令牌，根据指定的有效期和访问范围，开放受保护的资源访问<br>
上面的6个步骤中，B是关键，也就是客户端如何获取用户授权的问题，客户端只有拥有了用户授权，才能获取令牌，进而访问资源，下面
请看客户端获取用户授权的四种方式
## 客户端的授权模式
客户端必须得到用户的授权（authorization grant），才能获得令牌（access token）。OAuth2.0定义了四种授权方式
* 授权码模式（authorization code）
* 简化模式（implicit）
* 密码模式（resource owner password credentials）
* 客户端模式（client credentials）
### 授权码模式
授权码模式（authorization code）是功能最完整、流程最严密的授权模式。它的特点就是通过客户端的后台服务器，与”服务提供商”的认证服务器进行互动。
![](./images/authcode.png)
它的步骤如下：<br>
（A）用户访问客户端，后者将前者导向认证服务器。<br>
（B）用户选择是否给予客户端授权。<br>
（C）假设用户给予授权，认证服务器将用户导向客户端事先指定的”重定向URI”（redirection URI），同时附上一个授权码。<br>
（D）客户端收到授权码，附上早先的”重定向URI”，向认证服务器申请令牌。这一步是在客户端的后台的服务器上完成的，对用户不可见。<br>
（E）认证服务器核对了授权码和重定向URI，确认无误后，向客户端发送访问令牌（access token）和更新令牌（refresh token）。

下面是上面这些步骤所需要的参数。

A步骤中，客户端申请认证的URI，包含以下参数：

response_type：表示授权类型，必选项，此处的值固定为”code”
client_id：表示客户端的ID，必选项
redirect_uri：表示重定向URI，可选项
scope：表示申请的权限范围，可选项
state：表示客户端的当前状态，可以指定任意值，认证服务器会原封不动地返回这个值。
下面是一个例子。
```
GET /authorize?response_type=code&client_id=s6BhdRkqt3&state=xyz&redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb
HTTP/1.1
Host: server.example.com
```
C步骤中，服务器回应客户端的URI，包含以下参数：

code：表示授权码，必选项。该码的有效期应该很短，通常设为10分钟，客户端只能使用该码一次，否则会被授权服务器拒绝。该码与客户端ID和重定向URI，是一一对应关系。
state：如果客户端的请求中包含这个参数，认证服务器的回应也必须一模一样包含这个参数。
下面是一个例子
```
HTTP/1.1 302 Found
Location: <a href="https://client.example.com/cb">https://client.example.com/cb</a>?code=SplxlOBeZQQYbYS6WxSbIA
&amp;state=xyz
```
D步骤中，客户端向认证服务器申请令牌的HTTP请求，包含以下参数：

grant_type：表示使用的授权模式，必选项，此处的值固定为”authorization_code”。
code：表示上一步获得的授权码，必选项。
redirect_uri：表示重定向URI，必选项，且必须与A步骤中的该参数值保持一致。
client_id：表示客户端ID，必选项。
下面是一个例子。
```
POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&amp;code=SplxlOBeZQQYbYS6WxSbIA
&amp;redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb
```
E步骤中，认证服务器发送的HTTP回复，包含以下参数：

access_token：表示访问令牌，必选项。
token_type：表示令牌类型，该值大小写不敏感，必选项，可以是bearer类型或mac类型。
expires_in：表示过期时间，单位为秒。如果省略该参数，必须其他方式设置过期时间。
refresh_token：表示更新令牌，用来获取下一次的访问令牌，可选项。
scope：表示权限范围，如果与客户端申请的范围一致，此项可省略。
下面是一个例子。
```
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache

{
"access_token":"2YotnFZFEjr1zCsicMWpAA",
"token_type":"example",
"expires_in":3600,
"refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
"example_parameter":"example_value"
}
```
从上面代码可以看到，相关参数使用JSON格式发送（Content-Type: application/json）。此外，HTTP头信息中明确指定不得缓存。
### 简化模式
简化模式（implicit grant type）不通过第三方应用程序的服务器，直接在浏览器中向认证服务器申请令牌，跳过了”授权码”这个步骤，因此得名。所有步骤在浏览器中完成，令牌对访问者是可见的，且客户端不需要认证。
![](./images/jianhua.png)
