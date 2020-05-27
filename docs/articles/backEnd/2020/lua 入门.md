---
title: Lua 入门及问题回顾
date: 2020-05-27
author: FANLE
categories:
 - 后端
tags:
 - Lua
 - SpringBoot
---

这篇文章记录使用 Lua 遇到的问题

## 1. 基本介绍

基础的入门请详见下面连接

[Lua 30分钟入门](https://segmentfault.com/a/1190000022732306?utm_source=tuicool&utm_medium=referral)

## 2. 遇到问题

### 正则匹配问题

```lua

-- 这是登陆时候产生的 token ，然后通过正则进行匹配产出

local pi = "portal:user:login_token_a8b0b971-77c2-4e00-b994-86e8529b6d22"

-- %p 匹配符号

local name =  string.gsub(pi,"(.+token%p)(.+)","%2")

-- 输出

a8b0b971-77c2-4e00-b994-86e8529

```

### 数组遍历且查看是否含有值

```lua

local function IsInTable(value, tbl)
    for k,v in ipairs(tbl) do
        if v == value then
            return true;
        end
    end
    return false;
end

```


### 使用函数问题

在一个文件中使用 函数 的时候要定义为 local 函数, 否则会出现 函数未指明使用范围错误

```lua
local function square (List1,List2,bool)
    local Len = #List1;
    for i = 1, Len do
        local si = ""
        if bool then
            si = "shiro:session:login_token_"..string.gsub(tostring(List1[i]),regx,"%2")
        else
            si = "portal:user:login_token_"..string.gsub(tostring(List1[i]),regx,"%2")
        end
        print("si........."..si);
        -- 主要在这里
        if IsInTable(si,List2) then
            table.insert(result,si)
        end
    end
end

if portalKeysSize <= sessionKeysSize then
    square(portalKeys,sessionKeys,true)
    else
    square(sessionKeys,portalKeys,false)
end

```
### java中使用返回值问题

一般情况下 java 中返回的属于 Long 类型

```lua

local key = KEYS[1]

local arg = ARGV[1]

if key == arg then
  return 0
else 
  return 1
end
```



```lua
local result = {}

local tab = {"beijing", "shanghai"}

table.insert(result,tab)

local resultSize = #result

if (resultSize == 0) then
    return ''
else
    return result;
end

```

返回 Lua 的 table 类型,在 java 中使用 List 类型进行接收

```java

String LUA= "lua/getRedisMixed.lua";
Set<String> reidsKeys = redisService.getKeys("*");
List<String > list = new ArrayList<>(reidsKeys);
List result =  redisService.runLua(LUA,List.class,list);
log.info(result);
```

```java
// redisService 实现
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
    public boolean expire(String key, long expire) {
        return stringRedisTemplate.expire(key, expire, TimeUnit.SECONDS);
    }

    @Override
    public void remove(String key) {
        stringRedisTemplate.delete(key);
    }

    @Override
    public Long increment(String key, long delta) {
        return stringRedisTemplate.opsForValue().increment(key,delta);
    }

    @Override
    public Set<String> getKeys(String key) {
        return stringRedisTemplate.keys( key );
    }

    @Override
    public <T> T runLua(String fileClasspath, Class<T> returnType, List<String> keys, Object... values) {
        DefaultRedisScript<T> redisScript  = new DefaultRedisScript<>();
        ResourceScriptSource resourceScriptSource = new ResourceScriptSource(new ClassPathResource(fileClasspath));
        redisScript.setScriptSource(resourceScriptSource);
        redisScript.setResultType(returnType);
        return stringRedisTemplate.execute(redisScript,keys,values);
    }
}

```