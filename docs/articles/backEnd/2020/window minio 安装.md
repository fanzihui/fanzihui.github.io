---
title: window minio 安装使用
date: 2020-03-02
author: 范了饭饭
categories:
 - 后端
tags: 
 - minio
 - 插件
---

minio 可以用来坐 分布式 存储的一个库, 广泛运用于各个语言中!

## 1. 下载 minio

百度网盘连接:

链接: https://pan.baidu.com/s/1ZP-ajNvble1j7mvLnEkuhg 提取码: 2333

## 2. 使用 minio

```
// 记得创建文件夹呀
 ./minio.exe server D:/html/minio
```

## 3. 使用浏览器打开
出现以下结果时,可以使用浏览器打开地址 http://127.0.0.1:9000  , 输入AccessKey 和  SecretKey 即可！


```
λ ./minio.exe server D:/developer/minio
Endpoint:  http://169.254.113.222:9000  http://169.254.142.152:9000  http://192.168.109.65:9000  http://10.198.75.60:9000  http://192.168.94.55:9000  http://192.168.56.1:9000  http://192.168.124.1:9000  http://192.168.188.1:9000  http://127.0.0.1:9000
AccessKey: QAT8ZVQYOBM56SQ1WYFZ
SecretKey: ths0Tc2QgzBuq87PMlYXFg4AZpJi7FBPj7rzyq40

Browser Access:
   http://169.254.113.222:9000  http://169.254.142.152:9000  http://192.168.109.65:9000  http://10.198.75.60:9000  http://192.168.94.55:9000  http://192.168.56.1:9000  http://192.168.124.1:9000  http://192.168.188.1:9000  http://127.0.0.1:9000

Command-line Access: https://docs.min.io/docs/minio-client-quickstart-guide
   $ mc.exe config host add myminio http://169.254.113.222:9000 QAT8ZVQYOBM56SQ1WYFZ ths0Tc2QgzBuq87PMlYXFg4AZpJi7FBPj7rzyq40

Object API (Amazon S3 compatible):
   Go:         https://docs.min.io/docs/golang-client-quickstart-guide
   Java:       https://docs.min.io/docs/java-client-quickstart-guide
   Python:     https://docs.min.io/docs/python-client-quickstart-guide
   JavaScript: https://docs.min.io/docs/javascript-client-quickstart-guide
   .NET:       https://docs.min.io/docs/dotnet-client-quickstart-guide

```

## 4. 在 SpringBoot 中使用
具体的Minio 方法参考文档: https://docs.minio.io/cn/java-client-api-reference.html

定义需要传输的信息 dto/MinioUploadDto
```java
@Data
public class MinioUploadDto {
   // 文件名字
   private String name;
   // 文件上传地址
   private String url;
}
```
在 application.yml 中定义 minio 配置
```yml
minio:
  endpoint: http://localhost:9000 #MinIO服务所在地址
  bucketName: fan #存储桶名称
  accessKey: QAT8ZVQYOBM56SQ1WYFZ #访问的key
  secretKey: ths0Tc2QgzBuq87PMlYXFg4AZpJi7FBPj7rzyq40 #访问的秘钥
```
定义 Controller

```java
@Api(tags = "MinioController", description = "MinIO对象存储管理")
@Controller
@RequestMapping("/minio")
public class MinioController {
    private static final Logger LOGGER = LoggerFactory.getLogger(MinioController.class);

    @Value("${minio.endpoint}")
    private String ENDPOINT;
    @Value("${minio.bucketName}")
    private String  BUCKET_NAME;
    @Value("${minio.accessKey}")
    private String ACCESS_KEY;
    @Value("${minio.secretKey}")
    private String SECERT_KEY;

    @ApiOperation("文件上传")
    @RequestMapping(value = "/upload", method = RequestMethod.POST)
    @ResponseBody
    public CommonResult upload(@RequestParam("file") MultipartFile file) {
        try {
           // 创建minio 客户端连接
            MinioClient minioClient = new MinioClient(ENDPOINT,ACCESS_KEY,SECERT_KEY);
            // 判断 存储桶 是否存在
            boolean isExist = minioClient.bucketExists(BUCKET_NAME);
            if(isExist) {
                LOGGER.info("存储桶已经存在");
            } else {
                minioClient.makeBucket(BUCKET_NAME);
                // 设置存储桶权限
                minioClient.setBucketPolicy(BUCKET_NAME,"*.*", PolicyType.READ_WRITE);
            }
            // 获取文件名
            String filename = file.getOriginalFilename();
            // 这里使用到了 hutool 这个库，设置唯一名字
           String objectName =  SecureUtil.simpleUUID() + filename;
            // 上传文件
            minioClient.putObject(BUCKET_NAME,objectName,file.getInputStream(),file.getContentType());
            LOGGER.info("文件上传成功!");
            MinioUploadDto minioUploadDto = new MinioUploadDto();
            minioUploadDto.setName(filename);
            minioUploadDto.setUrl(ENDPOINT+"/"+BUCKET_NAME+"/"+objectName);
            return CommonResult.success(minioUploadDto);
        } catch (Exception e) {
            LOGGER.info("上传发生错误:{}",e.getMessage());
        }
        return CommonResult.failed();
    }
    @ApiOperation("文件删除")
    @RequestMapping(value = "/delete", method = RequestMethod.POST)
    @ResponseBody
    public CommonResult delete(@RequestParam("objectName") String objectName){
        try{
            MinioClient minioClient = new MinioClient(ENDPOINT,ACCESS_KEY,SECERT_KEY);
            minioClient.removeObject(BUCKET_NAME,objectName);
            return CommonResult.success(null,"删除成功");
        } catch (Exception e){
            e.printStackTrace();
        }
        return CommonResult.failed();
    }

    @ApiOperation("获取文件列表")
    @RequestMapping(value = "/list", method = RequestMethod.POST)
    @ResponseBody
    public CommonResult list(){
        try{
            MinioClient minioClient = new MinioClient(ENDPOINT,ACCESS_KEY,SECERT_KEY);
            boolean isBucketName = minioClient.bucketExists(BUCKET_NAME);
            List<Item> objList = new ArrayList<>();
            if(isBucketName) {
                Iterable<Result<Item>> objIterable = minioClient.listObjects(BUCKET_NAME);
                for(Result<Item> it: objIterable) {
                    Item item = it.get();
                    objList.add(item);
                }
                return CommonResult.success(objList);
            }
        } catch (Exception e){
            e.printStackTrace();
        }
        return CommonResult.failed();
    }
}

```
