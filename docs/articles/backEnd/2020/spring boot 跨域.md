---
title: spring boot 跨域
date: 2020-03-25
author: 范了饭饭
categories:
 - backEnd
tags: 
 - SpringBoot
 - 跨域
---

spring boot 跨域的 3 种方式:

## 1. 第1种方式(书写 config 配置文件)
```java
@Configuration
public class GlobalCorsConfig implements WebMvcConfigurer{
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")    // 配置映射路径
                .allowedOrigins("*")  // 允许跨域的域名，可以用*表示允许任何域名使用
                .allowedMethods("*")  // 允许任何方法（post、get等）
                .allowCredentials(true) // 带上cookie信息
                .maxAge(3600)           // 有效期间
                .allowedHeaders("*");  // 允许任何请求头
    }
}
```
或者这样写
```java
@Configuration
public class GlobalCorsConfig {

    @Bean
    public CorsFilter corsFilter(){
        CorsConfiguration configuration = new CorsConfiguration();
        configuration.addAllowedOrigin("*");  //允许跨域的域名，可以用*表示允许任何域名使用
        configuration.setAllowCredentials(true);  //带上cookie信息
        configuration.addAllowedHeader("*"); //允许任何请求头
        configuration.addAllowedMethod("*"); //允许任何方法（post、get等）
        UrlBasedCorsConfigurationSource source = new  UrlBasedCorsConfigurationSource();  // 存储request与跨域配置信息的容器
        source.registerCorsConfiguration("/**",configuration); // 注册容器配置信息
        return new CorsFilter(source);
    }
}
```
## 2. 第2种方式
```java
@WebFilter(filterName = "CorsFilter ")
@Configuration
public class CorsFilter implements Filter {
    @Override
    public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) throws IOException, ServletException {
        HttpServletResponse response = (HttpServletResponse) res;
        response.setHeader("Access-Control-Allow-Origin","*");
        response.setHeader("Access-Control-Allow-Credentials", "true");
        response.setHeader("Access-Control-Allow-Methods", "POST, GET, PATCH, DELETE, PUT");
        response.setHeader("Access-Control-Max-Age", "3600");
        response.setHeader("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
        chain.doFilter(req, res);
    }
}

```
## 3. 第3种方式(注解方式)
```java

public class GoodsController {

    @CrossOrigin(origins = "http://localhost:4000")
    @GetMapping("goods-url")
    public Response queryGoodsWithGoodsUrl(@RequestParam String goodsUrl) throws Exception {}
}   
```
