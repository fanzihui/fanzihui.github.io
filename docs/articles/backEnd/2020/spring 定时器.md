---
title: SpringBoot 定时器
date: 2020-03-29
author: 范了饭饭
categories:
 - backEnd
tags: 
 - SpringBoot
---


# SpringBoot 定时器

## 1. 设置 定时器 Config 配置

```java
/**
 * 定时任务配置
 * 
 */
@Configuration
@EnableScheduling
public class SpringTaskConfig {
}
```

## 2. 使用定时器
```java
@Component
public class TimeOutCancelTask {
    private Logger LOGGER = LoggerFactory.getLogger(TimeOutCancelTask.class);

    /**
     * cron表达式：Seconds Minutes Hours DayofMonth Month DayofWeek [Year]
     * 每10分钟扫描一次，扫描设定超时时间之前下的订单，如果没支付则取消该订单
     */
    @Scheduled(cron = "0 0/10 * ? * ?")
    private void cancelTimeOutOrder() {
        LOGGER.info("定时器的一些操作");
    }
}
```

## 3. 其他 
想要使用定时器必须要了解 Cron

#### Cron格式中每个时间元素的说明

|  时间元素    | 可出现字符  | 数值范围 |
|  ----       | ----    |  ----  | 
| Seconds	    | , - * / |	0-59
| Minutes	    | , - * / |	0-59
| Hours	      | , - * / |	0-23
| DayofMonth	| , - * / ? L W |	0-31
| Month	      | , - * / |	1-12
| DayofWeek	  | , - * / ? L # |	1-7或SUN-SAT

#### Cron格式中特殊字符说明
|字符	 | 作用	| 举例 
|   :----:       | ----    |  ----  | 
|,	| 列出枚举值	| 在Minutes域使用5,10，表示在5分和10分各触发一次
|-	| 表示触发范围	| 在Minutes域使用5-10，表示从5分到10分钟每分钟触发一次
|*	| 匹配任意值	| 在Minutes域使用*, 表示每分钟都会触发一次
|/	| 起始时间开始触发，每隔固定时间触发一次	| 在Minutes域使用5/10,表示5分时触发一次，每10分钟再触发一次
|?	| 在DayofMonth和DayofWeek中，用于匹配任意值	| 在DayofMonth域使用?,表示每天都触发一次
|#	| 在DayofMonth中，确定第几个星期几	| 1#3表示第三个星期日
|L	| 表示最后	| 在DayofWeek中使用5L,表示在最后一个星期四触发
|W	| 表示有效工作日(周一到周五)	|   在DayofMonth使用5W，如果5日是星期六，则将在最近的工作日4日触发一次