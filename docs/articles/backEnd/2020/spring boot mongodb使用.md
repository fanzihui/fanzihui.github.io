---
title: SpringBoot MongoDB 使用
date: 2020-03-29
author: 范了饭饭
categories:
 - backEnd
tags: 
 - SpringBoot
 - 数据库
 - MongoDB
---

# SpringBoot MongoDB 使用

如果不会安装MongoDB, 请参见文章 [ MongoDB 安装教程](https://fanle.netlify.com/views/backEnd/2020/MongoDB%20%E5%AE%89%E8%A3%85.html)

## 1. 引入依赖
```xml
<!---mongodb相关依赖-->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>

```
## 2. 在 application.yml 配置 mongodb

```yml
mongodb:
  host: localhost # mongodb的连接地址
  port: 27017 # mongodb的连接端口号
  database: read-history # mongodb的连接的数据库
```

## 3. 创建浏览记录实体

```java
// Document 标示映射到Mongodb文档上的领域对象
// Id       标示某个域为ID域
// Indexed  标示某个字段为Mongodb的索引字段

@Data
@Document
public class UserReadHistory {
    @Id
    private String id;
    @Indexed
    private Long userId;
    private String userName;
    @Id
    private Long booId;
    private Long booName;
}
```

## 4. 添加UserReadHistoryRepository接口用于操作Mongodb

```java

public interface UserReadHistoryRepository extends MongoRepository<UserReadHistory,String> {
    /**
     * 根据会员id按时间倒序获取浏览记录
     * @param userId 会员id
     */
    List<UserReadHistory> findByUserIdOrderByCreateTimeDesc(Long userId);
}

```

## 5. 创建 UserReadHistorySevice 接口 及 实现
```java
// 接口
public interface UserReadHistorySevice {

  // 创建记录
  int create(UserReadHistory userReadHistory);
  
  // 查看记录
  List<UserReadHistory> list(Long userId);

  // 批量删除记录(包含单个)
  int delete(List<String> ids);

}

```
```java
// 接口实现
@Service
public class UserReadHistoryServiceImpl implements UserReadHistoryService {
  
    @Autowired
    private UserReadHistoryRepository userReadHistoryRepository;

    @Override
    public int create(UserReadHistory userReadHistory) {
        userReadHistory.setId(null);
        userReadHistory.setCreateTime(new Date());
        userReadHistoryRepository.save(userReadHistory);
        return 1;
    }

    @Override
    public int delete(List<String> ids) {
        List<UserReadHistory> deleteList = new ArrayList<>();
        for(String id:ids){
            UserReadHistory userReadHistory = new UserReadHistory();
            userReadHistory.setId(id);
            deleteList.add(userReadHistory);
        }
        userReadHistoryRepository.deleteAll(deleteList);
        return ids.size();
    }

    @Override
    public List<UserReadHistory> list(Long userId) {
        return userReadHistoryRepository.findByUserIdOrderByCreateTimeDesc(userId);
    }

}

```

## 6. 添加UserReadHistoryController定义接口


```java 
@Controller
@RequestMapping("/user/readHistory")
public class UserReadHistoryController {
    @Autowired
    private UserReadHistoryService userReadHistoryService;

    @ApiOperation("创建浏览记录")
    @RequestMapping(value = "/create", method = RequestMethod.POST)
    @ResponseBody
    public CommonResult create(@RequestBody UserReadHistory userReadHistory) {
        int count = userReadHistoryService.create(userReadHistory);
        if (count > 0) {
            return CommonResult.success(count);
        } else {
            return CommonResult.failed();
        }
    }

    @ApiOperation("删除浏览记录")
    @RequestMapping(value = "/delete", method = RequestMethod.POST)
    @ResponseBody
    public CommonResult delete(@RequestParam("ids") List<String> ids) {
        int count = userReadHistoryService.delete(ids);
        if (count > 0) {
            return CommonResult.success(count);
        } else {
            return CommonResult.failed();
        }
    }

    @ApiOperation("展示浏览记录")
    @RequestMapping(value = "/list", method = RequestMethod.GET)
    @ResponseBody
    public CommonResult<List<userReadHistory>> list(Long userId) {
        List<UserReadHistory> list = userReadHistoryService.list(userId);
        return CommonResult.success(list);
    }
}


```