---
title: SpringBoot Test单元测试
date: 2023-07-18 09:34:12
tags:
- Java
- SpringBoot
- 单元测试
---



{% blockquote %}
不要忽视任何一个小的BUG，这将可能带来灾难
{% endblockquote %}



# 前言

## 单元测试的必要性

在工作中有时候会问同事关于单元测试，得到如下回答：

- 感觉项目挺赶工期的，就算想也没有时间写单元测试
- 项目业务简单，感觉没有写单元测试的必要
- 不知道如何写单元测试，还不如就直接调用接口测试
- 公司有专业测试人员，到时候让他们测就行了



这其实是对单元测试的一个误解。 单元测试可以反应出代码的水平，坚持编写单元测试可以在不经意间提高代码设计能力。 编写单元测试更是可以提升代码拆分、抽象的能力，让编程者写出更加优雅的代码。但这需要花费时间。

不得不承认的是，大多开发人员往往迫于项目进度压力，即使想编写单元测试，也是没有时间。对于一些开发完就很少维护的项目，开发完成即交付，单元测试的意义和价值确实不太大。这其实反映了公司管理层面的问题，当然这也要求开发者在有限的时间里，提升开发效率。

单元测试能够帮助我们在项目初期就发现和规避问题，排除一些隐藏bug，在新项目中推广和编写单元测试是非常有必要的。单元测试可以帮助我们降低测试成本和维护成本。



## Java单元测试工具

在Java中有非常多的单元测试的工具或框架可供选择：

- [JUnit](https://junit.org/junit5/)：Java中最有名、使用最广泛的单元测试框架

- [Spring Test](https://docs.spring.io/spring/docs/current/spring-framework-reference/testing.html): 使用 Spring Test 来对Spring相关的项目做单元测试，其中会结合或者集成其他测试框架和工具
- [spring-boot-starter-test](https://docs.spring.io/spring-boot/docs/2.0.2.RELEASE/reference/htmlsingle/#boot-features-testing): SpringBoot项目中的单元测试

本文选择使用 spring-boot-starter-test



# SpringBoot 2.x Test

## 创建SpringBoot 2.7.13项目

首先创建一个自己的SpringBoot 2.x项目，添加如下依赖：

- Spring Web
- MyBatis Plus
- Lombok

创建好工程后，在`pom.xml`里面可以看到项目自动引入了spring-boot-starter-test 依赖，这说明官方也是推荐项目使用单元测试。

### 添加依赖

`pom.xml`中依赖如下

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-starter</artifactId>
        <version>1.2.12</version>
    </dependency>
    <dependency>
        <groupId>com.mysql</groupId>
        <artifactId>mysql-connector-j</artifactId>
        <scope>runtime</scope>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
        <version>3.5.2</version>
    </dependency>
</dependencies>
```



### 添加配置

新增 `application.yml` 配置文件，在文件中添加配置如下：

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: your-db-url
    username: your-db-username
    password: your-db-password
    type: com.alibaba.druid.pool.DruidDataSource
    druid:
      validation-query: SELECT 1

server:
  port: 11000

mybatis-plus:
  # 对应的 XML 文件位置
  mapper-locations: classpath*:mapper/**/*Mapper.xml
  configuration:
    # 更详细的日志输出 会有性能损耗 org.apache.ibatis.logging.stdout.StdOutImpl
    # 关闭日志记录 (可单纯使用 p6spy 分析) org.apache.ibatis.logging.nologging.NoLoggingImpl
    # 默认日志输出 org.apache.ibatis.logging.slf4j.Slf4jImpl
    log-impl: org.apache.ibatis.logging.nologging.NoLoggingImpl
    # NONE：不启用 PARTIAL：只对非嵌套 resultMap 自动映射 FULL：对所有 resultMap 自动映射
    auto-mapping-behavior: full
    # MyBatis 自动映射时未知列或未知属性处理策
    # NONE：不做处理 WARNING：打印相关警告 FAILING：抛出异常和详细信息
    auto-mapping-unknown-column-behavior: none
    map-underscore-to-camel-case: true
  global-config:
    # 是否打印 Logo banner
    banner: true
    db-config:
      # AUTO 自增 NONE 空 INPUT 用户输入 ASSIGN_ID 雪花 ASSIGN_UUID 唯一 UUID
      id-type: auto
```



配置添加完成后，启动项目即可。



### 准备数据库、表

这里创建一个用户表进行演示

```sql
CREATE TABLE `user` (
  `id` int NOT NULL AUTO_INCREMENT,
  `username` varchar(36) COLLATE utf8mb4_general_ci DEFAULT NULL COMMENT '用户名',
  `nickname` varchar(36) COLLATE utf8mb4_general_ci DEFAULT NULL COMMENT '昵称',
  `raw_password` varchar(36) COLLATE utf8mb4_general_ci DEFAULT NULL COMMENT '明文密码',
  `sign` varchar(255) COLLATE utf8mb4_general_ci DEFAULT NULL COMMENT '个人签名',
  `status` tinyint DEFAULT NULL COMMENT '用户状态， 1：正常， 0：冻结',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;

-- 新增两条数据
INSERT INTO `test`.`user`(`id`, `username`, `nickname`, `raw_password`, `sign`, `status`) VALUES (1, 'mainac', 'pp', '123', 'Life Oriented Programming', 1);
INSERT INTO `test`.`user`(`id`, `username`, `nickname`, `raw_password`, `sign`, `status`) VALUES (2, 'wyd', 'dd', '234', 'MAGA', 2);

```

创建对应的Service、Mapper、Entity。 可以使用Mybatis Plus代码生成器，这里不过多介绍。

- 实体类：

```java
package me.wpl.springboottest.business.entity;

import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import lombok.Data;

@Data
@TableName("user")
public class User {

    @TableId(type = IdType.AUTO)
    private Integer id;
    private String username;
    private String nickname;
    private String rawPassword;
    private String sign;
	private Integer status;
    
}

```





## 示例

### 单元测试DAO/Mapper层

​	实际开发中，若采用了ORM框架，可以将DAO层视为功能的最小单元，从DAO层开始进行单元测试是有必要的。这里无视MybatisPlus自带的方法，提供一个示例查询

- UserMapper.java

```java
package me.wpl.springboottest.business.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import me.wpl.springboottest.business.entity.User;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface UserMapper extends BaseMapper<User> {
    
    @Select("SELECT * FROM user WHERE id = #{id}")
    User getById(@Param("id") Integer id);
}
```

- 测试类

分别测试查询到数据和未查询到数据的结果

```java
package me.wpl.springboottest;

import me.wpl.springboottest.business.entity.User;
import me.wpl.springboottest.business.mapper.UserMapper;
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;


@SpringBootTest
class UserMapperTest {

    @Autowired
    private UserMapper userMapper;

    @Test
    void testSelectNonNull() {
        User user = userMapper.getById(1);
        Assertions.assertNotNull(user);
    }

    @Test
    void testSelectNull() {
        User user = userMapper.getById(-1);
        Assertions.assertNull(user);
    }
}

```

每一个@Test对应方法都是一个单元测试，每个单元测试的情景都需要执行不报错。



### 单元测试Service层

实际开发中，Service层往往会依赖DAO层的一些方法， 如果DAO层方法单元测试通过了，那么在Service层就可以放心去使用，这时候只需关心Service层的代码是否出现bug。

- UserService & Impl

```java
package me.wpl.springboottest.business.service;

import com.baomidou.mybatisplus.extension.service.IService;
import me.wpl.springboottest.business.entity.User;

public interface IUserService extends IService<User> {
    
    User getById(Integer id);
}

```

```java
package me.wpl.springboottest.business.service.impl;

import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import me.wpl.springboottest.business.entity.User;
import me.wpl.springboottest.business.mapper.UserMapper;
import me.wpl.springboottest.business.service.IUserService;
import org.springframework.stereotype.Service;

@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements IUserService {
    
    @Override
    public User getById(Integer id) {
        if (id < 0) {
            throw new IllegalArgumentException("非法id");
        }
        // 其他处理逻辑
        return baseMapper.getById(id);
    }
    
}
```



- 测试类

```java
package me.wpl.springboottest;

import me.wpl.springboottest.business.entity.User;
import me.wpl.springboottest.business.service.IUserService;
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.test.context.SpringBootTest;

import javax.annotation.Resource;


@SpringBootTest(classes = SpringbootTestApplication.class)
public class UserServiceTest {

    @Autowired
    private IUserService userService;

    // 传参正确且查到数据
    @Test
    public void testSelectNonNull() {
        User user = userService.getById(1);
        Assertions.assertNotNull(user);
    }
	
    // 传参正确但查不到数据的情况
    @Test
    public void testSelectNull() {
        User user = userService.getById(10000);
        Assertions.assertNull(user);
    }
	
    // 测试错误传参的情况
    @Test
    public void testIllegalArgs() {
        Assertions.assertThrows(IllegalArgumentException.class, () -> userService.getById(-1));
    }

}

```



### 单元测试Controller层

- UserController

```java
package me.wpl.springboottest.business.controller;

import lombok.RequiredArgsConstructor;
import me.wpl.springboottest.business.entity.User;
import me.wpl.springboottest.business.service.IUserService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.Objects;


@RestController
@RequestMapping("/api/v1/user")
@RequiredArgsConstructor
public class UserController {

    private final IUserService userService;

    @GetMapping("/{id}")
    public ResponseEntity<User> getById(@PathVariable("id") Integer id) {
        return ResponseEntity.ok(userService.getById(id));
    }

}
```

- 测试类

```java
package me.wpl.springboottest;

import me.wpl.springboottest.business.controller.UserController;
import me.wpl.springboottest.business.entity.User;
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.ResponseEntity;

import javax.annotation.Resource;

@SpringBootTest
public class UserControllerTest {

    @Resource
    private UserController userController;

    @Test
    public void testGetUser() throws Exception {
        ResponseEntity<User> responseEntity = userController.getById(1);
        Assertions.assertTrue(responseEntity.getStatusCode().is2xxSuccessful());
        Assertions.assertNotNull(responseEntity.getBody());
    }
}
```

这里说明一下，单元测试针对最小功能点，Controller层同样作为SpringBean，直接进行Bean的方法测试即可。要通过Http测试接口，那么方法可就多了：

- 使用Postman、ApiFox等接口调式工具
- 另起一个单元测试类，通过HttpClient、RestTemplate等方式进行调用测试
- 通过MockMvc



# 总结

以上就是使用SpringBoot Test进行单元测试的全部介绍。

另外需要注意的是，如果编写了SpringBoot Test，在执行Maven的命令 mvn complie / mvn packge等命令的时候会执行项目中的单元测试，全部测试通过才会进行后续的mvn命令。 通过单元测试减少打包上线后出现问题的概率。



---



By Peilin,

***Life Oriented Programming***

