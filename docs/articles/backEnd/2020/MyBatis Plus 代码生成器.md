---
title: MyBatis Plus 代码生成器
date: 2020-03-27
author: 范了饭饭
categories:
 - 后端
tags: 
 - SpringBoot
 - 插件
---

## 1.引入依赖

```java
<properties>
  <mybatisplus.version>3.2.0</mybatisplus.version>
  <mybatisplusGenerator.version>3.3.1.tmp</mybatisplusGenerator.version>
</properties>
<!--mybatisplus-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>${mybatisplus.version}</version>
</dependency>
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>${mybatisplusGenerator.version}</version>
</dependency>
```
## 2. 书写 Config 文件
```java
@Configuration
@EnableTransactionManagement
@MapperScan("com.example.smbpauto.pms.mapper")
public class MyBatisPlusConfig {

    /**
     * 分页插件
     */
    @Bean
    public PaginationInterceptor paginationInterceptor(){
        return new PaginationInterceptor().setCountSqlParser(new JsqlParserCountOptimize(true));
    }
}
```

## 书写代码生成文件
```java
public class CodeGenerator {
    /**
     * <p>
     * 读取控制台内容
     * </p>
     */
    public static String scanner(String tip) {
        Scanner scanner = new Scanner(System.in);
        StringBuilder help = new StringBuilder();
        help.append("请输入" + tip + "：");
        System.out.println(help.toString());
        if (scanner.hasNext()) {
            String ipt = scanner.next();
            if (StringUtils.isNotEmpty(ipt)) {
                return ipt;
            }
        }
        throw new MybatisPlusException("请输入正确的" + tip + "！");
    }

    public static void main(String[] args) {
        // 代码生成器
        AutoGenerator mpg = new AutoGenerator();

        // 全局配置
        GlobalConfig gc = new GlobalConfig();
        String projectPath = System.getProperty("user.dir");
        gc.setOutputDir(projectPath + "/src/main/java");
        gc.setAuthor("fanfan");
        gc.setOpen(false);
//        gc.setEntityName("%sEntity");
        // gc.setSwagger2(true); 实体属性 Swagger2 注解
//        生成 mapper resultmap
        gc.setBaseResultMap(true);
        // 生成 mapper 里面的列名
//        gc.setBaseColumnList(true);
        mpg.setGlobalConfig(gc);

        // 数据源配置
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setUrl("jdbc:mysql://localhost:3306/mall?useUnicode=true&characterEncoding=utf-8&serverTimezone=Asia/Shanghai");
        // dsc.setSchemaName("public");
        dsc.setDriverName("com.mysql.cj.jdbc.Driver");
        dsc.setUsername("root");
        dsc.setPassword("root");
        mpg.setDataSource(dsc);

        // 包配置
        PackageConfig pc = new PackageConfig();
        pc.setModuleName(scanner("模块名"));
        pc.setParent("com.example.smbpauto");
        mpg.setPackageInfo(pc);

        // 自定义配置
        InjectionConfig cfg = new InjectionConfig() {
            @Override
            public void initMap() {
                // to do nothing
            }
        };

        // 如果模板引擎是 ibeetl 
        String templatePath = "/templates/mapper.xml.btl";
        // 自定义输出配置
        List<FileOutConfig> focList = new ArrayList<>();
        // 自定义配置会被优先输出
        focList.add(new FileOutConfig(templatePath) {
            @Override
            public String outputFile(TableInfo tableInfo) {
                // 自定义输出文件名 ， 如果你 Entity 设置了前后缀、此处注意 xml 的名称会跟着发生变化！！
                return projectPath + "/src/main/resources/mapper/" + pc.getModuleName()
                        + "/" + tableInfo.getEntityName() + "Mapper" + StringPool.DOT_XML;
            }
        });

        cfg.setFileOutConfigList(focList);
        mpg.setCfg(cfg);

        // 配置模板
        TemplateConfig templateConfig = new TemplateConfig();
        templateConfig.setXml(null);
        // 自定义配置 controller 模板
        templateConfig.setController("templates/controller2.java");
        mpg.setTemplate(templateConfig);

        // 策略配置
        StrategyConfig strategy = new StrategyConfig();
        strategy.setNaming(NamingStrategy.underline_to_camel);
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);
//        strategy.setSuperEntityClass("你自己的父类实体,没有就不用设置!");
        strategy.setEntityLombokModel(true);
        // 是否为构建者模型
        strategy.setEntityBuilderModel(true);
        strategy.setRestControllerStyle(true);
        // 公共父类
//        strategy.setSuperControllerClass("你自己的父类控制器,没有就不用设置!");
        // 写于父类中的公共字段
        strategy.setSuperEntityColumns("id");
        strategy.setInclude(scanner("表名，多个英文逗号分割").split(","));
        strategy.setControllerMappingHyphenStyle(true);
//        strategy.setTablePrefix(pc.getModuleName() + "_");
        mpg.setStrategy(strategy);
        mpg.setTemplateEngine(new BeetlTemplateEngine());
        mpg.execute();
    }
}

```

## 4. 自定义 controller 模板
文件配置在 resources/templates 目录下, 文件名成: controller2.java.btl ; 

```java
package ${package.Controller};

import com.baomidou.mybatisplus.extension.api.ApiController;
import com.baomidou.mybatisplus.extension.api.R;
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;


import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import ${package.Service}.${table.serviceName};
import ${package.Entity}.${entity};

import java.io.Serializable;
import java.util.List;

<% if(restControllerStyle){ %>
import org.springframework.web.bind.annotation.RestController;
<% }else{ %>
import org.springframework.stereotype.Controller;
<% } %>
<% if(isNotEmpty(superControllerClassPackage)){ %>
import ${superControllerClassPackage};
<% } %>

/**
 * <p>
 * ${table.comment!} 前端控制器
 * </p>
 *
 * @author ${author}
 * @since ${date}
 */
<% if(restControllerStyle){ %>
@RestController
<% }else{ %>
@Controller
<% } %>
@RequestMapping("<% if(isNotEmpty(package.ModuleName)){ %>/${package.ModuleName}<% } %>/<% if(isNotEmpty(controllerMappingHyphenStyle)){ %>${controllerMappingHyphen}<% }else{ %>${table.entityPath}<% } %>")
<% if(kotlin){ %>
class ${table.controllerName}<% if(isNotEmpty(superControllerClass)){ %> : ${superControllerClass}()<% } %>
<% }else{ %>
    <% if(isNotEmpty(superControllerClass)){ %>
public class ${table.controllerName} extends ${superControllerClass} {
    <% }else{ %>
public class ${table.controllerName} extends ApiController {
    @Autowired
    public ${table.serviceName} i${entity}Service;

    /**
         * 分页查询所有数据
         *
         * @param page 分页对象
         * @param ${entity} 查询实体
         * @return 所有数据
         */
        @GetMapping
        public R selectAll(Page<${entity}> page,${entity} ${table.entityPath}) {
            return success(this.i${entity}Service.page(page, new QueryWrapper<>(${table.entityPath})));
        }

    /**
         * 通过主键查询单条数据
         *
         * @param id 主键
         * @return 单条数据
         */
        @GetMapping("{id}")
        public R selectOne(@PathVariable Serializable id) {
            return success(this.i${entity}Service.getById(id));
        }

        /**
             * 新增数据
             *
             * @param ${entity}  实体对象
             * @return 新增结果
             */
            @PostMapping
            public R insert(@RequestBody ${entity} ${table.entityPath}) {
                return success(this.i${entity}Service.save( ${table.entityPath}));
            }

        /**
             * 修改数据
             *
             * @param  ${entity} 实体对象
             * @return 修改结果
             */
            @PutMapping
            public R update(@RequestBody ${entity} ${table.entityPath}) {
                return success(this.i${entity}Service.updateById(${table.entityPath}));
            }

            /**
             * 删除数据
             *
             * @param idList 主键结合
             * @return 删除结果
             */
            @DeleteMapping
            public R delete(@RequestParam("idList") List<Long> idList) {
                return success(this.i${entity}Service.removeByIds(idList));
            }


    <% } %>

}
<% } %>
```
