---
title: "深入理解mybatis(一)，mybatis初始化机制初探"
date: "2018-05-30 14:52"
categories: [mybatis]
tags: [深入理解mybatis]
---
本篇文章主要介绍mybatis的初始化机制，从配置文件加载一直到初始化完成的过程。
<!-- more -->

# demo
让我们先来写一个简单的mybatis操作mysql数据库的demo

1.创建一个maven项目，并引入mybatis,mysql依赖
```
<dependency>
		<groupId>mysql</groupId>
		<artifactId>mysql-connector-java</artifactId>
		<version>5.1.46</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
		<artifactId>mybatis</artifactId>
		<version>3.4.6</version>
</dependency>
```
2.在源码根目录下创建配置文件
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="com.mysql.jdbc.Driver" />
				<property name="url" value="jdbc:mysql://localhost:3306/test?useSSL=false" />
				<property name="username" value="root" />
				<property name="password" value="123456" />
			</dataSource>
		</environment>
	</environments>
	<mappers>
		<mapper resource="com/mtoliv/mybatis/mapper/BlogMapper.xml" />
	</mappers>
</configuration>
```
3.创建model，BlogMapper.xml文件和BlogMapper.java文件
```
public class Blog implements Serializable {

	private static final long serialVersionUID = -4958441314416958973L;

	private Long id;

	private String name;

	public Long getId() {
		return id;
	}

	public void setId(Long id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}
}
```
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.mtoliv.mybatis.dao.BlogMapper">
	<select id="selectBlog" resultType="com.mtoliv.mybatis.model.Blog">
		select * from Blog where id = #{id}
	</select>
</mapper>
```
```
public interface BlogMapper {

	public Blog selectBlog(Long id);
}
```
4.初始化，调用接口，获取数据
```
public static void main(String[] args) throws IOException {

		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession session = sqlSessionFactory.openSession();
		BlogMapper blogMapper = session.getMapper(BlogMapper.class);
		Blog blog = blogMapper.selectBlog(1L);
		System.out.println(blog.getName());
	}
```
# mybatis-config配置文件解析源码
1. 使用XMLConfigBuilder解析xml文件，读出其中的各个配置项，处理成Configuration对象
```
private void parseConfiguration(XNode root) {
    try {
      //issue #117 read properties first
      propertiesElement(root.evalNode("properties"));
      Properties settings = settingsAsProperties(root.evalNode("settings"));
      loadCustomVfs(settings);
      typeAliasesElement(root.evalNode("typeAliases"));
      pluginElement(root.evalNode("plugins"));
      objectFactoryElement(root.evalNode("objectFactory"));
      objectWrapperFactoryElement(root.evalNode("objectWrapperFactory"));
      reflectorFactoryElement(root.evalNode("reflectorFactory"));
      settingsElement(settings);
      // read it after objectFactory and objectWrapperFactory issue #631
      environmentsElement(root.evalNode("environments"));
      databaseIdProviderElement(root.evalNode("databaseIdProvider"));
      typeHandlerElement(root.evalNode("typeHandlers"));
      mapperElement(root.evalNode("mappers"));
    } catch (Exception e) {
      throw new BuilderException("Error parsing SQL Mapper Configuration. Cause: " + e, e);
    }
  }
```
2. 使用Configuration对象，创建DefaultSqlSessionFactory
```
public SqlSessionFactory build(Configuration config) {
    return new DefaultSqlSessionFactory(config);
  }
```
这两步完成后，一个SqlSessionFactory就创建出来了

# 相关设计模式
创建者模式：在解析xml，生成Configuration的过程中，由XMLConfigBuilder解析xml文件并生成Configuration,这为后面直接通过java配置mybatis运行环境提供了便利
工厂模式：DefaultSqlSessionFactory使用Configuration对象创建会话工厂，负责管理会话的创建，销毁等动作。
