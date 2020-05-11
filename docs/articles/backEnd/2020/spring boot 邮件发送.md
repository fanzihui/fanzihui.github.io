---
title: SpringBoot 邮件发送
date: 2020-05-11
author: FANLE
categories:
 - 后端
tags: 
 - SpringBoot
---

## 1. 配置邮箱服务

在 qq 邮箱 或者 163 邮箱 种配置用户的 POP3/SMTP 服务, 并开启服务, 获取随机授权码

```
163 邮箱: 
服务器地址：
POP3服务器: pop.163.com
SMTP服务器: smtp.163.com
IMAP服务器: imap.163.com
```

```
QQ 邮箱: 
服务器地址：
POP3服务器: pop.qq.com
SMTP服务器: smtp.qq.com
IMAP服务器: imap.qq.com
```

## 2. SpringBoot 集成邮件发送

1. 配置依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>

```

2. 配置文件

```yml

spring:
  mail:
    // 主机地址
    host: smtp.qq.com
    // 账号
    username: 123456@qq.com
    // 密码
    password: 4512215
    // 其他属性
    properties:
      mail:
        smtp:
          socketFactory:
            class: javax.net.ssl.SSLSocketFactory
##         ssl:
##           enable :true
    // 默认编码
    default-encoding: utf-8

```

3. 邮件发送


- 简单发送
```java
// JavaMailSenderImpl 就是一个SpringBoot中用来发送邮件的一个实现类，我们需要将它注入到bean中，以供使用。

@RestController
public class MailController {
    @Autowired
    JavaMailSenderImpl javaMailSender;
    @RequestMapping("/mail")
    public String sendMail(){
        SimpleMailMessage message = new SimpleMailMessage();
        //邮件设置
        message.setSubject("邮件主题");
        message.setText("邮件内容");
        message.setTo("xxxxxxx@139.com","111111111@qq.com");
        message.setFrom("qzstudynote@qq.com");
        javaMailSender.send(message);
        return "简单邮件发送成功！"
    }
}

```

- 带附件发送
```java
@RequestMapping("/mineMail")
public String sendMineMail() throws MessagingException {
    //1、创建一个复杂的邮件
    MimeMessage mimeMessage = javaMailSender.createMimeMessage();
    MimeMessageHelper helper = new MimeMessageHelper(mimeMessage, true);
    //邮件主题
    helper.setSubject("这是一个邮件啊");
    //文本中添加图片
    helper.addInline("image1",new FileSystemResource("D:\\images\\spring\\1.jpg"));
    //邮件内容
    helper.setText("全栈学习笔记<a href='https://fanzihui.github.io/'>百度一下</a>    <img src='cid:image1'></img>",true);
    helper.setTo("xxxxx@139.com");
    helper.setFrom("123456@qq.com");
    //附件添加图片
    helper.addAttachment("1.jpg",new File("D:\\images\\spring\\1.jpg"));
    //附件添加word文档
    helper.addAttachment("哈哈哈.docx",new File("D:\\images\\spring\\哈哈哈.docx"));

    javaMailSender.send(mimeMessage);
   return "复杂邮件发送！";
}


```
代码说明：

创建一个MimeMessage 邮件，但是我们也需要创建一个工具类MimeMessageHelper，相当于代理类吧，邮件的属性配置就由这个工具类来实现。

addInline() ,第一个参数是一个contentId,String类型的，相当于是一个key,第二个参数是一个Resource对象，资源对象，这里我们传了一个本地的图片就用的FileSystemResource对象。当然这里是说的我们用的这个addInline方法的参数是这样，还有其他的参数类型，所谓重载。

setText() ，这里用到的第一个参数就是文本字符串，第二个就是是否解析文本中的html语法。

addAttachment() 这个方法是用来添加附件的，附件和我们之前添加的图片不一样，附件作为一种未下载的文件，而资源文件则是直接显示到正文中。利用我自己的邮箱进行测试的截图




- 使用 thymeleaf 模板邮件发送

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

```html
// 创建模板
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>

<p th:text="${name}"></p>
<a th:text="这是一个链接" th:href="${link}"></a>
<img th:src="${image1}">
</body>
</html>

```

```java
// 创建 controller

@RequestMapping("/thyMail")
    public String sendThymeleafMail() throws MessagingException {
        MimeMessage mimeMessage = javaMailSender.createMimeMessage();
        MimeMessageHelper messageHelper = new MimeMessageHelper(mimeMessage);
        messageHelper.setSubject("这是一个thymeleaf模板邮件");
        messageHelper.setTo("xxxxxxxx@139.com");
        messageHelper.setFrom("qzstudynote@qq.com");
        Context context = new Context();
        context.setVariable("name","这是一个新建的thymeleaf模板");
        context.setVariable("link","https://www.cnblogs.com/swzx-1213/");
        context.setVariable("image1","https://s1.ax1x.com/2020/04/14/JShDYt.th.jpg");
        String value = templateEngine.process("email.html",context);
        messageHelper.setText(value,true);
        javaMailSender.send(mimeMessage);
        return "模板邮件发送成功";
    }

```
代码说明：
Context 属于org.thymeleaf.context这个包。

context.setVariable() ,第一个参数是String,第二个是Object类型。第一个参数就对应thymeleaf模板上面相同名字的参数。

templateEngine.process() 将指定路径的html文件转换成String类型返回。


## 3. 总结
SpringBoot 发送邮件是一个基础的常用功能, 另外可以搭配时间验证邮件是否过期,或者发送激活码等操作。