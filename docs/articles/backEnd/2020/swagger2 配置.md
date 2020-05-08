---
title: spring boot swagger2 生成接口文档
date: 2020-03-25
author: 范了饭饭
categories:
 - 后端
tags: 
 - SpringBoot
 - swagger2
---

spring boot swagger2 生成接口文档

## 1. 添加依赖
```xml 
<!--Swagger-UI API文档生产工具-->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.6.1</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.6.1</version>
</dependency>

```

## 2. config 中配置 
```java

@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket createRestApi(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                //为当前包下controller生成API文档
                .apis(RequestHandlerSelectors.basePackage("com.example.sredis.controller"))
                //为有@Api注解的Controller生成API文档
//                .apis(RequestHandlerSelectors.withClassAnnotation(Api.class))
                //为有@ApiOperation注解的方法生成API文档
//                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("xx 网站接口文档")
                .description("学习 redis 教程")
                .version("1.0")
                .build();
    }
}

```
## 3. Controller 中使用

```java
@Controller
@Api(tags = "UesrController", description = "会员登录注册管理")
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserService userService;

    @ApiOperation("获取验证码")
    @RequestMapping(value = "/gc", method = RequestMethod.GET)
    @ResponseBody
    public CommonResult getAuthCode(@RequestParam String telephone){
        return userService.generateAuthCode(telephone);
    }

    @ApiOperation("判断验证码是否正确")
    @RequestMapping(value = "/vc", method = RequestMethod.POST)
    @ResponseBody
    public CommonResult updatePassword(@RequestParam String telephone,
                                       @RequestParam String authCode) {
        return userService.verifyAuthCode(telephone,authCode);
    }
}


```