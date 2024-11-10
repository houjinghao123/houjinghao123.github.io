+++
title = 'SpringBoot配置热部署的几种方式'
date = 2024-11-08T23:09:14+08:00
categories = ["SpringBoot"]
tags = ["小技巧","SpringBoot"]


+++
# SpringBoot热部署

最近在写一些项目在进行调试和修改总需要手动重启很麻烦，于是就想到了对项目进行热部署。然后就在网上搜集了一些热部署相关的内容，在这里总结一下，方便自己使用。

## 使用devTools来进行热部署

###1. Spring DevTools

Spring DevTools是Spring团队开发的一个模块，旨在提供开发时的快速迭代和调试支持。它包含以下主要功能：

- **热部署：** 在代码更改后，自动重新加载应用上下文，使更改立即生效。
- **内嵌服务器支持：** 支持内嵌服务器（如Tomcat）的快速重启。
- **全局配置文件热加载：** 允许在不重启应用的情况下更新全局配置文件。
- **禁用特定缓存：** 可以选择性地禁用特定的缓存，以便更快地看到代码更改的效果。

### 2.配置Spring DevTools

#### 2.1初始化一个maven项目

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.example</groupId>
  <artifactId>HotStart1</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>

  <name>HotStart1</name>
  <url>http://maven.apache.org</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

#### 2.2添加Spring DevTools相关依赖

```xml
    <!--导入热部署依赖-->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-devtools</artifactId>
      <scope>runtime</scope>
    </dependency>
```

#### 2.3在配置文件中添加相关配置信息

```yaml
#devtools配置
spring:
  devtools:
    restart:
      #开启热部署
      enabled: true
      #热部署重新加载java下面类文件
      additional-paths: src/main/java
      #排除静态文件重新部署
      #exclude: resources/**
```

#### 2.4对idea进行相关配置

打开设置进行如下配置

![](https://i.postimg.cc/QMhjssBX/screenshot-64.png)

再然后打开注册表(Ctrl+Shift+Alt+/)

![](https://i.postimg.cc/jd2tvK99/screenshot-65.png)

### 3.开始测试

在项目中创建一个controller包，在创建一个类

```java
@RestController
public class indexController {
    @GetMapping("/hello")
    @ResponseBody
    public String index(){
        System.out.println("接口请求成功");
        int a=0;
        return "hell123";
    }
}
```

创建一个启动类

```java
@SpringBootApplication
public class App 
{
    public static void main( String[] args )
    {
        SpringApplication.run(App.class);
    }
}
```

然后启动

![](https://i.postimg.cc/wv5LWDt6/screenshot-66.png)

项目正常启动，然后我们使用浏览器进行访问

![](https://i.postimg.cc/bv5pK5tg/screenshot-67.png)

访问正常后我们可以尝试对indexController里面的方法进行修改

![](https://i.postimg.cc/CxWcXHKD/screenshot-68.png)

当我们修改完并且切换到浏览器界面后就会发现项目已经自动加载了。

此时在对该接口访问就会发现内容已经发生了改变。

![](https://i.postimg.cc/L6PvcQJT/screenshot-69.png)
