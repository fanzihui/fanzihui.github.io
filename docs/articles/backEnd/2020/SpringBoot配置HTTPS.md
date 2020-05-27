---
title: SpringBoot配置HTTPS
date: 2020-05-27
author: FANLE
categories:
 - 后端
tags:
 - SpringBoot
---

SpringBoot配置HTTPS,并实现HTTP访问自动转HTTPS访问



生成服务器端证书

```

keytool -genkey -alias tomcat -keyalg RSA -keystore ./server.keystore 

```


生成客户端证书

```

keytool -keystore server.keystore -export -alias tomcat -file ./server.cer

```


配置 springBoot

```yml
server:
  port: 8085
  ssl:
    key-store-password: 123456
    key-alias: tomcat
    key-store: classpath:server.keystore
    enabled: true
    key-store-type: JKS

```

配置 Config 

```java

@Configuration
@Profile("dev")
public class SSLConfig {

    @Value("${server.port}")
    private int sslPort;//https的端口

    /**
      * @Description: 重定向请求到端口,需要端口转发时使用
      * @Author: fanfan
      * @Date: 2020-05-19  15:47
      */
    @Bean
    public Connector connector(){
      Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
      connector.setScheme("http");
      connector.setPort(80);
      connector.setSecure(false);
      connector.setRedirectPort(sslPort);
      return connector;
    }

    /**
      * @Description:  重定向到HTTPS
      * @Author: fanfan
      * @Date: 2020-05-19  15:47
      */
    @Bean
    TomcatServletWebServerFactory tomcatServletWebServerFactory(){
        TomcatServletWebServerFactory factory = new TomcatServletWebServerFactory(){
            @Override
            protected void postProcessContext(Context context) {
                SecurityConstraint constraint = new SecurityConstraint();
                constraint.setUserConstraint("CONFIDENTIAL");
                SecurityCollection collection = new SecurityCollection();
                collection.addPattern("/*");
                constraint.addCollection(collection);
                context.addConstraint(constraint);
            }
        };
//        factory.addAdditionalTomcatConnectors(connector());
        return factory;
    }
}


```


tip

```
// 查看端口

netstat -ano

// 查看进程

tasklist|findstr (查看到的进程号)
# simple
C:\Users\Administrator>tasklist|findstr 3664
vmware-hostd.exe              3664 Services                   0      5,040 K

```