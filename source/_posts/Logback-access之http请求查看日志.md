---
title: Logback-access之http请求查看日志
date: 2017-04-07 10:17:38
categories:
- 技术
- Log
tags: 
- logback
- logger
---
参考：{% link logback官网 https://logback.qos.ch/access.html [external] [title] %}
原本以为logback access是用http来访问查看logback日志文件的,但是后来发现不是这个用途，主要是用来记录和存储外部http请求信息的日志功能。
但是还是要记录下，此处按照官网的配置
1.配置tomcat配置文件
$TOMCAT_HOME/conf/server.xml的<Engine>的<Host> 节点下陪配置 （$TOMCAT_HOME是tomcat的路径）
默认会去当前目录需找logback-access.xml配置文件
```command
<Valve className="ch.qos.logback.access.tomcat.LogbackValve"/> 
```
或者指定目录寻找配置文件
```command
<Valve className="ch.qos.logback.access.tomcat.LogbackValve" quiet="true" filename="c:/my-logback-access.xml"/>
```
注意：如果你使用的是eclipse集成tomcat启动项目，此时修改tomcat的配置文件要删除Servers重新配置，否则server配置文件的修改将会被重置
<!-- more -->
2.配置项目web.xml文件
```java
  <servlet>
    <servlet-name>AccessViewStatusMessages</servlet-name>
    <servlet-class>ch.qos.logback.access.ViewStatusMessagesServlet</servlet-class>
  </servlet>

  <servlet-mapping>
    <servlet-name>AccessViewStatusMessages</servlet-name>
    <url-pattern>/lbAccessStatus</url-pattern>
  </servlet-mapping>
```
3.启动tomcat
控制台出现类似下列输出，则表示配置成功
```command
21:56:09,921 |-INFO in c.q.lb.access.j.a.ConfigurationAction - Ignoring debug attribute.
21:56:09,921 |-INFO in c.q.lb.core.j.a.AppenderAction - About to instantiate appender of type [ch.qos.logback.core.ConsoleAppender]
21:56:09,921 |-INFO in c.q.lb.core.j.a.AppenderAction - Naming appender as [STDOUT]
21:56:10,015 |-INFO in c.q.lb.core.j.a.AppenderAction - Popping appender named [STDOUT] from the object stack
21:56:10,015 |-INFO in c.q.lb.core.j.a.AppenderRefAction - Attaching appender named [STDOUT] to ch.qos.logback.access.tomcat.LogbackValve[Catalina]
21:56:10,015 |-INFO in c.q.lb.access.j.a.ConfigurationAction - End of configuration.
```
打开浏览器访问：http://主机地址：端口/项目名/lbAccessStatus
![Logback-Access](/img/lbAccessStatus.jpg)






