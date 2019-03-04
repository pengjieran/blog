---
title: SpringBoot是如何做到自动化配置的？
tags:
  - SpringBoot
categories:
  - 'Spring,SpringBoot'
toc: false
date: 2019-03-04 21:20:17
---
SpringBoot是如何做到自动化配置的？做到只需要一个Enable***就能开启指定功能的？相信你看完这篇博客，能有一个清晰的认识。
<!-- more -->
# 开题
大家在新建立一个SpringBoot应用的时候，首先看见的一个注解就是@SpringBootApplication，这个注解里面究竟干了什么事，为什么加了这个注解才能启动？本篇博客将为你一一展开。
# @SpringBootApplication
开始第一个SpringBoot应用的时候首先看见的一个注解就是这个，当你进入其源代码查看的时候发现也没有写什么，别急，看它上面的注解。
```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
		@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
```
大家会发现，里面又多了三个注解，第一个注解标明当前是一个SpringBoot应用，第二个注解表明要打开自动配置，第三个注解表明要扫描用户指定的包，重点看第二个注解，
进入以后会发现，最终引入的一个类是AutoConfigurationImportSelector，这个类里面都干了什么呢？
完整看完这个类的代码后你会发现，最重要的代码其实是这段：
```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata,
			AnnotationAttributes attributes) {
		List<String> configurations = SpringFactoriesLoader.loadFactoryNames(
				getSpringFactoriesLoaderFactoryClass(), getBeanClassLoader());
		Assert.notEmpty(configurations,
				"No auto configuration classes found in META-INF/spring.factories. If you "
						+ "are using a custom packaging, make sure that file is correct.");
		return configurations;
	}
```
加载工厂类，也就是从META-INF/spring.factories配置文件中读入各个工厂类，比如读入的org.springframework.boot.env.YamlPropertySourceLoader，就是支持yml格式配置文件的
