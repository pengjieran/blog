---
title: SpringCloudLearn系列（一）：服务注册中心eureka版
date: 2017-12-10 19:38:58
categories: 
    - springcloud
tags: 
    - eurekaserver
    - eureka服务端配置
---

本文是SpringCloudLearn系列的第一篇，前段时间一直在做微服务的落地工作，相对稳定后，现在开始整理SpringCloud的一些最佳实践给大家分享，有不对的地
方希望大家指出。这一系列的文章会基于SpringCloud最新的Finchley.M5版本给出示例，后续也会及时更新到最新的版本，相关完整示例请转向到以下地址：
[[SpringCloudLearn系列示例](https://github.com/pengjieran/SpringCloudLearn)]

<!-- more -->

## 初始化项目
1. 新建maven项目
2. pom.xml完整配置：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>spring-cloud-eureka-server</artifactId>
	<version>1.0.0-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>spring-cloud-eureka-server</name>
	<description>springcloud eureka server端项目</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.0.M7</version>
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
		<spring-cloud.version>Finchley.M5</spring-cloud.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
		</dependency>
		<!--eurekaserver暂时无法切换成undertow容器，已向官方提issue -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<configuration>
					<executable>true</executable>
				</configuration>
			</plugin>
			<!-- 跳过测试 -->
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-surefire-plugin</artifactId>
				<configuration>
					<skip>true</skip>
				</configuration>
			</plugin>
		</plugins>
	</build>

	<repositories>
		<repository>
			<id>spring-snapshots</id>
			<name>Spring Snapshots</name>
			<url>https://repo.spring.io/snapshot</url>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</repository>
		<repository>
			<id>spring-milestones</id>
			<name>Spring Milestones</name>
			<url>https://repo.spring.io/milestone</url>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</repository>
	</repositories>

	<pluginRepositories>
		<pluginRepository>
			<id>spring-snapshots</id>
			<name>Spring Snapshots</name>
			<url>https://repo.spring.io/snapshot</url>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</pluginRepository>
		<pluginRepository>
			<id>spring-milestones</id>
			<name>Spring Milestones</name>
			<url>https://repo.spring.io/milestone</url>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</pluginRepository>
	</pluginRepositories>
</project>
```
3. 在包下新建SpringCloudEurekaServerApplication.java文件
4. 在该类上增加注解并配置相关信息,全部内容如下：
```java
package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer
public class SpringCloudEurekaServerApplication {

	public static void main(String[] args) {
		
		SpringApplication.run(SpringCloudEurekaServerApplication.class, args);
	}
}
```

5. 在src/main/resources目录下增加bootstrap.yml文件，主要配置如下，至于为何不用application.yml,等你真正使用于生产环境就会明白。
```yaml
spring:
  application:
    name: discovery
    admin:
      enabled: true

server:
  port: 8761 #端口号，eureka服务端默认端口号是8761
  undertow:
    io-threads: 4 #io线程数，一般与cpu核心数相同
    worker-threads: 200 #工作线程数
    
eureka: 
  client:
    register-with-eureka: false #服务端不需要注册自身
    fetch-remote-regions-registry: false
    fetch-registry: false
  server: 
    enable-self-preservation: false #是否打开安全模式，默认为true,生产环境请设置为true,开发和测试环境由于需要频繁发布，可设置为false
    
```

好了，到这里，所有的配置工作就完成了

## 编译并运行
1. 打开命令行并运行：
```bash
mvn spring-boot:run
```
也可以在STS中直接使用boot dashboard启动。

建议：开发Springcloud相关项目可直接使用官方提供的STS，简单，高效，不建议使用IDEA

好了，一个简单的eureka服务注册中心到这里就运行起来了，本篇文章结束。