---
title: Lombok 常用注解
date: 2020-03-27
author: 范了饭饭
categories:
 - backEnd
tags: 
 - SpringBoot
 - 插件
---

## 1. 引入依赖
配合 spring boot 使用时

```java
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
  <optional>true</optional>
</dependency>

```

## 2. 基本代码
```java
@Data 
@Accessors(chain = true)
public class User implements Serializable {

  private static final long serialVersionUID = 1L;

  private Integer id;

  private String name;

  private String loginname;

  private String pwd;

  private String address;

  private Integer sex;

  private String remark;

  private Integer deptid;

  @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
  private Date hiredate;
}

```
## 3. 解释
```java
// 可以生成 getter setter 方法,作用于类上，是以下注解的集合：@ToString @EqualsAndHashCode @Getter
@Data 
// 生成不同的 equals, hashCode 方法
@EqualsAndHashCode(callSuper = false)
// 使用后可以使用链式写法
@Accessors(chain = true)
// 生成 toString() 方法
@ToString
// 生成没有字段的构造方法
@NoArgsConstructor
// 生成带有所有字段的构造方法
@AllArgsConstructor
// 使用 Builder 生成, 调用时候
// xx.builder().setName("xx").setSex("boy").build();
@Builder
```