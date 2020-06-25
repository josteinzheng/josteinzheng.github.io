---
layout:     post
title:      "How to build Tomcat by maven"
subtitle:   ""
date:       2020-06-10
author:     "Jostein"
tags:
    - java
    - maven
    - manual
    - tomcat
---
<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [本地环境和软件版本](#%E6%9C%AC%E5%9C%B0%E7%8E%AF%E5%A2%83%E5%92%8C%E8%BD%AF%E4%BB%B6%E7%89%88%E6%9C%AC)
- [添加maven配置](#%E6%B7%BB%E5%8A%A0maven%E9%85%8D%E7%BD%AE)
- [编译](#%E7%BC%96%E8%AF%91)
- [启动](#%E5%90%AF%E5%8A%A8)
- [FAQ](#faq)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

### 本地环境和软件版本
* 操作系统版本：　Ubuntu 18.04.4 LTS
* Tomcat: [8.5.56](
https://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8/v8.5.56/src/apache-tomcat-8.5.56-src.tar.gz)
* JDK: 11 [Linux/x64 Java Development Kit](https://download.java.net/openjdk/jdk11/ri/openjdk-11+28_linux-x64_bin.tar.gz)　或者　```sudo apt install openjdk-11-jdk```
* Intellij IDEA 2019 Ultimate Edition

### 添加maven配置
　　解压缩tomcat源码包后，在解压出的源码根目录```apache-tomcat-8.5.55-src```下创建pom.xml文件，内容如下：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.apache.tomcat</groupId>
    <artifactId>apache-tomcat-8.5.55-src</artifactId>
    <name>Tomcat8.5</name>
    <version>8.5</version>
    <build>
        <!--指定源目录-->
        <finalName>Tomcat8.5</finalName>
        <sourceDirectory>java</sourceDirectory>
        <resources>
            <resource>
                <directory>java</directory>
            </resource>
        </resources>
        <plugins>
            <!--引入编译插件-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                    <source>11</source>
                    <target>11</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <!--tomcat 依赖的基础包-->
    <dependencies>
        <dependency>
            <groupId>org.easymock</groupId>
            <artifactId>easymock</artifactId>
            <version>3.4</version>
        </dependency>
        <dependency>
            <groupId>ant</groupId>
            <artifactId>ant</artifactId>
            <version>1.7.0</version>
        </dependency>
        <dependency>
            <groupId>wsdl4j</groupId>
            <artifactId>wsdl4j</artifactId>
            <version>1.6.2</version>
        </dependency>
        <dependency>
            <groupId>javax.xml</groupId>
            <artifactId>jaxrpc</artifactId>
            <version>1.1</version>
        </dependency>
        <dependency>
            <groupId>org.eclipse.jdt.core.compiler</groupId>
            <artifactId>ecj</artifactId>
            <version>4.5.1</version>
        </dependency>
        <dependency>
            <groupId>javax.xml.soap</groupId>
            <artifactId>javax.xml.soap-api</artifactId>
            <version>1.4.0</version>
        </dependency>
    </dependencies>
</project>
```
然后在IDEA中导入该工程，选择maven(添加pom.xml后应该是默认选项)
### 编译
　　IDEA上选择Build->Build Project，会出现如下提示信息
```
/home/jostein/workspace/apache-tomcat-8.5.55-src/java/org/apache/catalina/mbeans/JmxRemoteLifecycleListener.java
Error:(514, 53) java: package sun.rmi.registry is not visible
  (package sun.rmi.registry is declared in module java.rmi, which does not export it to the unnamed module)
```
这个问题从出错信息上应该是sun.rmi.registry包在这个jdk版本已经不对外暴露，不过出错的类```JmxRegistry```从调用上看已经不再使用了，所以可以直接删除该调用和类定义即可，原代码调用如下：
```java
try {
    /*
     * JmxRegistry is registered as a side-effect of creation.
     * This object is here so we can tell the IDE it is OK for it
     * not to be used.
     */
    @SuppressWarnings("unused")
    JmxRegistry unused = new JmxRegistry(theRmiRegistryPort, registryCsf, registrySsf, "jmxrmi", jmxServer);
} catch (RemoteException e) {
    log.error(sm.getString(
            "jmxRemoteLifecycleListener.createRegistryFailed",
            serverName, Integer.toString(theRmiRegistryPort)), e);
    return null;
}
```
删除后重新编译通过。
### 启动
　　编译通过后，找到```org.apache.catalina.startup.Bootstrap```类，点击左侧三角图标选择```Debug 'Bootstrap.main()'```可成功启动，在浏览器输入[http://localhost:8080](http://localhost:8080)提示HTTP状态 500 - 内部服务器错误，详细错误堆栈如下：
```
消息 Unable to compile class for JSP
org.apache.jasper.JasperException: Unable to compile class for JSP
	org.apache.jasper.JspCompilationContext.compile(JspCompilationContext.java:613)
	org.apache.jasper.servlet.JspServletWrapper.service(JspServletWrapper.java:399)
	org.apache.jasper.servlet.JspServlet.serviceJspFile(JspServlet.java:386)
	org.apache.jasper.servlet.JspServlet.service(JspServlet.java:330)
	javax.servlet.http.HttpServlet.service(HttpServlet.java:741)
根本原因。
java.lang.NullPointerException
	org.apache.jasper.compiler.Validator$ValidateVisitor.<init>(Validator.java:523)
	org.apache.jasper.compiler.Validator.validateExDirectives(Validator.java:1855)
	org.apache.jasper.compiler.Compiler.generateJava(Compiler.java:221)
	org.apache.jasper.compiler.Compiler.compile(Compiler.java:375)
	org.apache.jasper.compiler.Compiler.compile(Compiler.java:351)
	org.apache.jasper.compiler.Compiler.compile(Compiler.java:335)
	org.apache.jasper.JspCompilationContext.compile(JspCompilationContext.java:597)
	org.apache.jasper.servlet.JspServletWrapper.service(JspServletWrapper.java:399)
	org.apache.jasper.servlet.JspServlet.serviceJspFile(JspServlet.java:386)
	org.apache.jasper.servlet.JspServlet.service(JspServlet.java:330)
	javax.servlet.http.HttpServlet.service(HttpServlet.java:741)
```
这个问题是未配置jsp解析引擎，找到```org.apache.catalina.startup.ContextConfig```类，在```protected synchronized void configureStart()```方法里添加配置
```java
webConfig();  //原有代码
//配置jsp解析引擎
context.addServletContainerInitializer(new JasperInitializer(), null);
```
　　重启服务器后，再次请求http://localhost:8080应可正常显示
### FAQ
　　无
