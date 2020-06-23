---
layout:     post
title:      "Tomcat体系结构"
subtitle:   ""
date:       2020-06-22
author:     "Jostein"
tags:
    - java
    - backend
    - web
    - middleware
    - tomcat
---

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Summarize](#summarize)
- [Whole Picture](#whole-picture)
  - [Coyote](#coyote)
    - [ProtocolHandler](#protocolhandler)
    - [Processor](#processor)
    - [Adapter](#adapter)
  - [Connector](#connector)
  - [Endpoint](#endpoint)
  - [Container](#container)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Summarize
　　Tomcat是一个servlet容器，同时也是web应用服务器，下文基于tomcat8.5.50版本简要分析Tomcat是如何设计来承担这两个角色的。
## Whole Picture
![tomcat architecture](/img/tomcat-architecture.png)
如上图所示，左侧是web应用服务器的主要组件，右侧是servlet容器的核心组件，中间Connector承上启下构成了整个tomcat。下面将具体说明各个组件的核心功能。
### Coyote
　　基于nio/apr/nio2框架处理socket连接，支持协议http1.1/http2.0/ajp，并封装请求为Request和返回结果Response（coyote框架内部使用）,将http请求转发给Adapter实现类。
 * #### ProtocolHandler
　　顶层协议接口，主要实现类AbstractAjpProtocol，AbstractHttp11Protocol，根据不同协议来初始化Request请求。
 * #### Processor
　　将封装好的Request和Response转发给Adapter实现类。
 * #### Adapter
　　servlet容器实现该接口来处理请求和返回结果。

### Connector
  web服务器和servlet容器的连接器，也支持ajp协议来连接外部web服务器（如Apache服务器）。
### Endpoint
　　封装socket处理结果和io事件并回调给ProtocolHandler。
### Container
　　容器顶层接口，主要子接口Engine,Host,Context,Wrapper,请求会经过各父容器的加工、过滤，最终转发到wrapper封装的servlet。
 * #### Engine
　　Catalina层级中的最顶层父容器，其下可包含多个Host或Context
 * #### Host
　　代表一个虚拟站点，一般来说对应一个hostname，可包含多个Context
 * #### Context
　　代表一个独立的web应用，可包含多个Wrapper
 * #### Wrapper
   负责一个servlet类整个生命周期的管理，方便实现或配置针对该servlet的拦截器
