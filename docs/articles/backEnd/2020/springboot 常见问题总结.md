---
title: SpringBoot 常见问题总结(不断更新)
date: 2020-05-07
author: FANLE
categories:
 - 后端
tags:
 - SpringBoot
---

## 1. 连接数据库时时区问题:
```

jdbc:mysql://localhost:3306/mp_student?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf8&useSSL=false

```

## 2. 常见的包分类
```
	java
		config      存放Java配置
		component   存放组件
		common      存放通用类,如工具类和通用返回结果
		controller  存放控制器
		mapper/dao  存放自定义的 mapper
		entity      实体层
		service     业务逻辑接口层
		  impl      业务逻辑接口实现层
 	resources
 		mapper      自定义数据库操作语句
    static      静态文件
    theme       主题文件
    application.yml   基础配置文件
```
## 3. 常用的框架
	mybatis plus   数据库访问调用
	druid    设置数据库连接池
	swagger2 生成接口文档
	shiro  角色权限管理

## 4. shiro 授权时类型无法转换,请注释掉 pom.xml spring-boot-devtools 这个依赖, 原因是 ClassLoader类加载器的不同导致的类型转换异常。

## 5. https 设置
```
// 生成证书
keytool -genkeypair -alias tomcat -storetype PKCS12 -keyalg RSA -keysize 2048  -keystore keystore.p12 -validity 3650
// application.yml 配置 ssl
server:
  port: 8080
  ssl:
    key-store: classpath:keystore.p12
    key-store-type: PKCS12
    key-alias: tomcat
    key-store-password: 123456
```

## 6. 整合Swagger-UI
添加依赖

```
<!--Swagger-UI API文档生产工具-->
<dependency>
  <groupId>io.springfox</groupId>
  <artifactId>springfox-swagger2</artifactId>
  <version>2.7.0</version>
</dependency>
<dependency>
  <groupId>io.springfox</groupId>
  <artifactId>springfox-swagger-ui</artifactId>
  <version>2.7.0</version>
</dependency>
// 配置文件
/**
 * Swagger2API文档的配置
 */
@Configuration
@EnableSwagger2
public class Swagger2Config {
    @Bean
    public Docket createRestApi(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                //为当前包下controller生成API文档
                .apis(RequestHandlerSelectors.basePackage("com.macro.mall.tiny.controller"))
                //为有@Api注解的Controller生成API文档
//                .apis(RequestHandlerSelectors.withClassAnnotation(Api.class))
                //为有@ApiOperation注解的方法生成API文档
//                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("SwaggerUI演示")
                .description("mall-tiny")
                .contact("macro")
                .version("1.0")
                .build();
    }
}
```
## 7. docker pull 超时解决方法
```
vim /etc/hosts
100.24.246.89 registry-1.docker.io
```
## 8. github windows 设置
```
用户名：
$ git config --global user.name "fanfan"

邮箱：
$ git config --global user.email "1025537699@qq.com"

记住提交的邮箱及密码：
$ git config --global credential.helper store
```

## 9. LocalDateTime 时区问题
```
修改application.yml 中的MySQL 时区
url: jdbc:mysql://127.0.0.1:3306/warehouse?useUnicode=true&characterEncoding=utf8&useSSL=true&serverTimezone=Hongkong&useSSL=false
```

## 10.  //密码 下面这句话输出时让属性显示不出来
```
    @JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    private String password;
```

## 11. shiro 权限下未登录时候返回json数据

  注意返回的一定是JSON.toJSONString()格式

## 12. fastjson 中空值无法保存问题
```
String jsonStr = JSON.toJSONString(json,SerializerFeature.WriteMapNullValue);

转换各种类型: https://blog.csdn.net/john1337/article/details/76275797
```

## 13. tomcat 部署时候出现 websocket 问题


## 14. 单点登录问题
  注意使用的Cache 

## 16. RequestBody RequestParam 区别
请求包json 和 表单区别

## 17. mssql 版本与 LocalDateTime 问题
```
升级mssql 版本
<dependency>
  <groupId>com.microsoft.sqlserver</groupId>
  <artifactId>mssql-jdbc</artifactId>
  <version>8.2.0.jre8</version>
</dependency>
```

## 18. shiro rememberme记住我


## 19. fastjson 数组转换
```
eg: [{name: "fanfan"}]

转换：  JSONArray array =  JSONArray.parseArray(msg.toString());
        for (int i = 0 ; i < array.size(); i++){
            ReceiveParam receiveParam = JSONObject.parseObject(array.get(i).toString(),ReceiveParam.class);
            System.out.println(receiveParam.getTableName());
            ctx.write(receiveParam.getGuid());
        }

eg: {list: [{name: "fanfan"}]}

转换:    JSONObject json = JSON.parseObject(msg.toString());
        JSONArray array = json.getJSONArray("list");
        for (int i = 0 ; i < array.size(); i++){
            ReceiveParam receiveParam = JSONObject.parseObject(array.get(i).toString(),ReceiveParam.class);
            System.out.println(receiveParam.getTableName());
            ctx.write(receiveParam.getGuid());
        }

eg: {name: "fanfan"}

转换为实体:  ReceiveParam receiveParam = JSON.parseObject(msg.toString(),ReceiveParam.class);

```

## 20. Mybatis plus - 映射字段时排除不必要的字段，忽略字段

```
1、声明该字段是 transient 的
private transient  Integer a;
1
2、声明该字段是 static 的
private static Integer a;
1
3、通过注解声明该字段不是一个数据库表里面的字段
@TableField(exist = false)
private transient  Integer a;
```

## 21. druid 无法打开
去掉账号和密码即可打开

## 22. netty 接收字段长度太长
```
server:
    .option(ChannelOption.SO_BACKLOG, 65535)

    .childOption(ChannelOption.RCVBUF_ALLOCATOR, new FixedRecvByteBufAllocator(65535))

NettyServerHandlerInitializer:

    .addLast(new DelimiterBasedFrameDecoder(65535,delimiter))
```

## 23. mvn clean package -DskipTests

## 24. 出现左连接为 null 是可以使用查询语句:

```
eg:

select
        pi.Patient_ID,
        pi.Ward as patientInfoWard,
        pi.Name,
        pi.Sex,
        pi.EaseMode,
        pi.BedNo as patientInfoBedNo,
        pi.AnesthesiaMode as anesthesiaMode,
        pi.Doctor,
        pi.PumpCode as piPumpCode,
        pi.PatientCode as piPatientCode,
        pi.Operation, p.* from Pump p
        LEFT JOIN  PatientInfo pi
        on p.PumpCode = pi.PumpCode and p.PatientCode = pi.PatientCode 
        LEFT JOIN Users u
        on u.SysHospital_ID = p.SysHospital_ID
        where
         (
            p.SysHospital_ID =  '2'
            and
            p.SysProduct_ID = '1'
            and
            u.UserCode='1'
            and
            pi.PatientCode is not null
            and 
            pi.PumpCode is not null
          )
          
        ORDER BY p.StateFlag desc,pi.Ward asc, pi.BedNo asc
```

## 25. 判断时间大小

```
import cn.hutool.core.date.DateUtil;
import java.util.Date;

public Boolean pumpTimeDiff(LocalDateTime t1,LocalDateTime t2){
        System.out.println("t1: "+ t1);
        System.out.println("t1: "+ t2);
        Date dateT1 = DateUtil.parse(t1.toString().replaceAll("T"," "));
        Date dateT2 = DateUtil.parse(t2.toString().replaceAll("T"," "));
        if(dateT1.getTime() < dateT2.getTime()){
            System.out.println("True,可插入数据库");
            return true;
        } else {
            System.out.println("False,不可插入数据库");
            return false;
        }
    }

```

## 26. 常见 SqlServer 语句

```
// 根据时间排序查询最新一条数据
SELECT top 1 *  FROM Pump ORDER BY LastUploadTime desc;

```

## 27. String 类型转换成 Boolean 类型

```
Boolean.parseBoolean(str)

```