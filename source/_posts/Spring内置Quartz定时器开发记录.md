---
title: Spring内置Quartz定时器开发记录
date: 2017-11-09 14:14:38
categories:
- 技术
- Spring
- Quartz
tags: 
- Spring
- Quartz
- Cron
- FixedDelay
- FixedRate
---
spring内置的quartz定时器使用记录，主要记录实现使用配置，三种部署计划任务方式和定时器cron表达式配置
<h3 id="h31">两种方法实现使用配置</h3><h4 id="h3h41">方法一：基于xml配置的方式实现</h4><h5 id="h3h41h51">1.编写普通的DOJO实体bean</h5>{% codeblock lang:java %}
package com.bus.test.task;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

import com.bus.test.controller.WxInterface;

public class AccessTokenTask{
	private static Logger logger = LoggerFactory.getLogger(AccessTokenTask.class);
	
	public void scheduled() throws Exception {
		String accessToken = WxInterface.getAccessToken();
		WxInterface.accessToken = accessToken;
		logger.info("定时器刷新AccessToken:{}",accessToken);
	}
}
{% endcodeblock %}<!-- more --><h5 id="h3h41h52">2.配置xml配置文件</h5>声明task命名空间 
xmlns:task="http://www.springframework.org/schema/task"
声明schema约束task名称空间的标签规范
http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-3.2.xsd
{% codeblock lang:xml %}
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:p="http://www.springframework.org/schema/p"
	......
	xmlns:task="http://www.springframework.org/schema/task" 
	xsi:schemaLocation="
	http://www.springframework.org/schema/beans 
	http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
	......
	http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-3.2.xsd"  
	default-autowire="byName" default-lazy-init="false">
    
    <!-- 将内注入到Spring容器 -->
    <bean id="accessTokenTask" class="com.bus.test.task.AccessTokenTask"></bean>  
    <!-- 在applicationContext.xml中进行配置，使用定时器
        ref : pojo类的名称
        method : 调用的方式名称
        cron : cronExpression表达式
        cron="0/5 * * * * ?"  //表示五秒钟执行一次,详解见下面的 定时器cron表达式配置
     -->
    <task:scheduled-tasks>
        <task:scheduled ref="accessTokenTask" method="scheduled" cron="0 0 0 ? * SUN"/>
        <!-- 配置多个定时器 -->
        <task:scheduled ref="......" method="..." cron="0/5 * * * * ?"/>
    </task:scheduled-tasks>
</beans>
{% endcodeblock %}<h4 id="h3h42">方法二：基于注解@Scheduled实现</h4><h5 id="h3h42h51">1.配置xml配置文件</h5>声明task命名空间 
xmlns:task="http://www.springframework.org/schema/task"
声明schema约束task名称空间的标签规范
http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-3.2.xsd
{% codeblock lang:xml %}
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	......
	xmlns:task="http://www.springframework.org/schema/task" 
	xsi:schemaLocation="
	......
	http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-3.2.xsd"  
	default-autowire="byName" default-lazy-init="false">
	
    <!-- 开启任务注解驱动支持 -->
    <task:annotation-driven/>
    
</beans>
{% endcodeblock %}<h5 id="h3h42h52">2.编写基于注解的类和基于注解的方法</h5>{% codeblock lang:java %}
package com.bus.test.task;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import com.bus.test.controller.WxInterface;
/**
 * 此出需要注意，AccessTokenTask类一定要记得扫描，注入到Spring容器定时器才会生效
 */
@Component
public class AccessTokenTask{
	private static Logger logger = LoggerFactory.getLogger(AccessTokenTask.class);
	/**
	 * cron 执行任务方式 详见下面内容
	 */
	@Scheduled(cron="0 0 0 ? * SUN") //每个星期天的0点0分0秒 0 0 0 ? * SUN，MON，TUE，WED，THU，FRI，SAT
	public void scheduled() throws Exception {
		String accessToken = WxInterface.getAccessToken();
		WxInterface.accessToken = accessToken;
		logger.info("定时器刷新AccessToken:{}",accessToken);
	}
}
{% endcodeblock %}
<h3 id="h32">fixedRate、fixedDelay、cron部署计划任务方式</h3>fixedRate=1000 * 60  // 每隔多少毫秒执行一次任务
{% blockquote %}
long org.springframework.scheduling.annotation.Scheduled.fixedDelay()
Execute the annotated method with a fixed period between the end of the last invocation and the start of the next.
Returns:
the delay in milliseconds{% endblockquote %}
fixedDelay=1000 * 60 // 执行一次任务完毕之后，延迟多少毫秒再执行该任务。
{% blockquote %}long org.springframework.scheduling.annotation.Scheduled.fixedDelay()
Execute the annotated method with a fixed period between the end of the last invocation and the start of the next.
Returns:
the delay in milliseconds{% endblockquote %}
cron=0  0  0  ?  *  SUN   // 表达式配置了该任务在什么时候执行
{% blockquote %}String org.springframework.scheduling.annotation.Scheduled.cron()
A cron-like expression, extending the usual UN*X definition to include triggers on the second as well as minute, hour, day of month, month and day of week. e.g. "0 * * * * MON-FRI" means once per minute on weekdays (at the top of the minute - the 0th second).
Returns:
an expression that can be parsed to a cron schedule
{% endblockquote %}
<h3 id="h33">定时器cron表达式配置</h3>Cron表达式有6-7位以空格隔开的字符串表达式，分别对应 
"秒 分 时 天/月 月 天/星期"
"秒 分 时 天/月 月 天/星期 年"
秒	 	0-59	 	
分	 	0-59	 	
小时	 	0-23	 	
日期	 	1-31	 	
月份	 	1-12 或者 JAN-DEC
星期	 	1-7 或者 SUN-SAT	
年      [可选]1970-2099

【\* / # ? L - ,】等特殊值：
1、MON 一个星期中的周几：
 SUN，MON，TUE，WED，THU，FRI，SAT 周天-周五
 "0 0 12 ? &#42; WED" = 每星期三中午12点执行 
 "0 10,44 14 ? 3 SAT" = 每年三月的星期六的下午2:10和2:44执行 
 
2、* 字符代表所有可能的值：
 月中表示每个月，年中表示每年，星期中表示每天
 "0 0 &#42; &#42; &#42; &#42;" = 每天每小时执行
 "0 0 12 &#42; &#42; ?" = 每天中午12点执行 
 
3、/ 字符用来指定数值的增量，起始时间开始触发，然后每隔固定时间执行一次:
 “3/10”表示从第3分钟开始，每10分钟执行（它和“3，13，23，33，43”的含义一样）
 "/10 &#42; &#42; &#42; &#42; &#42;" = 每天每10秒钟执行
 "0 0/5 14 &#42; &#42; ?" = 每天下午2点到下午2:55期间的每5分钟执行
 "0 3/10 14 &#42; &#42; ?" = 每天下午2点的3，13，23，33，43,53执行  
 
4、? 字符表示不指定值，仅被用于月和星期两个子表达式:
 月和星期指定值以后，为了避免冲突，需要将另一个子表达式的值设为?
 "0 0 0 25 12 ?" = 12月25日（圣诞节）午夜执行
 "0 15 10 15 &#42; ?" = 每月15日上午10:15执行 
 
5、L 字符表示最后，仅被用于月和星期：
 6L表示倒数第6天，FRIL表示这个月的最一个星期五，在使用“L”参数时，不要指定列表或范围
 "0 15 10 L &#42; ?" = 每月最后一日的上午10:15执行 
 "0 15 10 ? &#42; 6L" = 每月的最后一个星期五上午10:15执行 
 "0 15 10 ? &#42; 6L 2002-2005" = 2002年至2005年的每月的最后一个星期五上午10:15执行 

6、- 字符表示范围:
 0-5表示从5分到20分钟每分钟执行一次
 "0 0 8-10 &#42; &#42; &#42;" = 每天的8，9，10点执行 
 "0 0 9-17 &#42; &#42; MON-FRI" = 周一到周五早9点-晚5点每小时执行
 "0 0-5 14 &#42; &#42; ?" = 每天下午2点到下午2:05期间的每分钟执行 
 
7、, 字符表示枚举值：
 枚举值的时间执行任务
 "0 0/5 14,18 &#42; &#42; ?" = 在每天下午2点到2:55期间和下午6点到6:55期间的每5分钟执行
 "0 10,44 14 ? 3 WED" = 每年三月的星期三的下午2:10和2:44执行 

 
8、# 表示确定每个月第几个星期几，只能出现在 天/月 的位置。
 4#2，表示某月的第二个星期三
 "0 15 10 ? &#42; 6#3" = 每月的第三个星期五上午10:15执行


 



