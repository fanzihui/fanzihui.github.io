---
title: spring boot redis 缓存
date: 2020-03-25
author: 范了饭饭
categories:
 - backEnd
tags: 
 - SpringBoot
 - Redis
---

SpringBoot 使用 Redis 缓存数据

## 1. 配置依赖

```xml
<!-- redis 依赖 -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

```
## 2. 设置 application.yml redis

```yml
server:
  port: 8080
spring:
  redis:
    host: localhost
    password: 123456
    database: 0
    port: 6379
    timeout: 3000ms # 连接超时时间（毫秒）
    jedis:
      pool:
        max-active: 20 # 连接池最大连接数（使用负值表示没有限制）
        max-wait: -1ms # 连接池最大阻塞等待时间（使用负值表示没有限制）
        max-idle: 8 # 连接池中的最大空闲连接
        min-idle: 0 # 连接池中的最小空闲连接
redis:
  key:
    prefix:
      authCode: "portal:authCode:"
    expire:
      authCode: 120 # 验证码超期时间

```
## 3. 配置 redis 接口 及 实现
StringRedisTemplate继承RedisTemplate,使用的是StringRedisSerializer,[有更多的方法可供使用](https://www.cnblogs.com/slowcity/p/9002660.html)

```java
// redis 接口
public interface RedisService {

    // 设置数据
    void set(String key, String value);

    // 获取数据
    String get(String key);

    // 设置过期时间
    boolean expire(String key, long expire);

    // 删除数据
    void remove(String key);

    // 自增操作
    Long increment(String key, long delta);
}
```
```java
// redis 接口实现
@Service
public class RedisServiceImpl implements RedisService {

    
    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @Override
    public void set(String key, String value) {
        stringRedisTemplate.opsForValue().set(key,value);
    }

    @Override
    public String get(String key) {
        return stringRedisTemplate.opsForValue().get(key);
    }

    @Override
    public void remove(String key) {
        stringRedisTemplate.delete(key);
    }

    @Override
    public boolean expire(String key, long expire) {
        return stringRedisTemplate.expire(key, expire, TimeUnit.SECONDS);
    }

    @Override
    public Long increment(String key, long delta) {
        return stringRedisTemplate.opsForValue().increment(key,delta);
    }
}
```
## 4. 配置 common 返回数据

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Accessors(chain = true)
@ToString
public class CommonResult<T> {
    private Integer code;
    private String msg;
    private T data;

    public static final Integer OK=200;
    public static final Integer ERROR=-1;

    // 成功结果
    public static <T> CommonResult<T> success(T data, String msg) {
        return new CommonResult<T>(OK, msg, data);
    }


    // 失败结果
    public static <T> CommonResult<T> failed(String msg) {
        return new CommonResult<T>(ERROR, msg, null);
    }
}
```


## 4. 配置 service 

```java
public interface UserService {

    /**
     * 生成验证码
     */
    CommonResult generateAuthCode(String telephone);

    /**
     * 判断验证码和手机号码是否匹配
     */
    CommonResult verifyAuthCode(String telephone, String authCode);
}

```
```java
@Service
public class UserServiceImpl implements UserService {

    @Autowired
    private RedisService redisService;

    @Value("${redis.key.prefix.authCode}")
    private String REDIS_KEY_PREFIX_AUTH_CODE;

    @Value("${redis.key.expire.authCode}")
    private Long AUTH_CODE_EXPIRE_SECONDS;

    @Override
    public CommonResult generateAuthCode(String telephone) {
        StringBuilder sb = new StringBuilder();
        Random random = new Random();
        for (int i = 0; i < 6; i++) {
            sb.append(random.nextInt(10));
        }
        //验证码绑定手机号并存储到redis
        redisService.set(REDIS_KEY_PREFIX_AUTH_CODE + telephone, sb.toString());
        redisService.expire(REDIS_KEY_PREFIX_AUTH_CODE + telephone, AUTH_CODE_EXPIRE_SECONDS);
        return CommonResult.success(sb.toString(), "获取验证码成功");

    }

    @Override
    public CommonResult verifyAuthCode(String telephone, String authCode) {
        if (StringUtils.isEmpty(authCode)) {
            return CommonResult.failed("请输入验证码");
        }
        String realAuthCode = redisService.get(REDIS_KEY_PREFIX_AUTH_CODE + telephone);
        boolean result = authCode.equals(realAuthCode);
        if (result) {
            return CommonResult.success(null, "验证码校验成功");
        } else {
            return CommonResult.failed("验证码不正确");
        }
    }
}
```

## 5. 配置 contoller

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
## 6. 测试缓存
成功后打开地址即可测试 http://localhost:8080/swagger-ui.html


