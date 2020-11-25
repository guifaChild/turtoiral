# 的觐见的进件的规则的存储

---

## 规则引擎理解

由于业务来创建查询语句，程序对数据库进行查询

![](/assets/test.png)

---

## 规则引擎的开发

### 规则引擎

1、申请人之前有多少个逾期的进件

```
match (p:Person)-[h:HAS_APPLICATION]->(a:Application) where a.status="VOER_DUE" and p.persid="24301" return 

count(a)
```

2、申请人的一度关系中有多少个触碰黑名单

```
match(p:Person)-[]-(p1:Person)-[h:HAS_PHONE]-(b:Black) where p.personid="243010"

### return p,p1,h,b

count（b）

##这里的b是电话的另一个标签
```

2、申请人的二度关系中有多少个触碰黑名单

```
match(p:Person)-[]-(p1:Person)-[]-(p:Person)-[h:HAS_PHONE]-(b:Black) where p.personid="243010"

### return p,p1,h,b

count（b）
```

## 规则引擎代码开发

SpringBoot就是极少的配置跑去了以前spring框架的繁琐的配置项

* 新建maven工程

```
Groupid：com.sft.ai.kg

artifactid:rule-engineer-server
```

* 引包

```
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>2.2.5.RELEASE</version>

        </dependency>
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
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.16.16</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>6.0.6</version>
        </dependency>
        <dependency>
            <groupId>org.neo4j.driver</groupId>
            <artifactId>neo4j-java-driver</artifactId>
            <version>1.6.3</version>
        </dependency>
</dependencies>
```

* 创建Applicaiton.java，程序的入口

```
package com.sft.ai;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableScheduling;
//入口类标签
@EnableScheduling
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class,args);
    }
}
```

* 创建配置swagger 

```
package com.sft.ai.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

/**
 * 配置文件，swagger-ui写起来更加的简单
 */
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket api(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .pathMapping("/")
                .select()
                .paths(PathSelectors.regex("/.*"))
                .build();
    }

    private ApiInfo apiInfo(){
        return new ApiInfoBuilder().title("Knowledge Gragh Rule Engine Service Api")
                .contact(new Contact("zhangguifa","","zhangguifa5@gmail.com"))
                .description("Knowledge Gragh Rule Engine Service Api")
                .version("1.0")
                .build();
    }
}
```

* 日志的配置logback.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <include resource="org/springframework/boot/logging/logback/defaults.xml"/>
    <property name="FILE_LOG_PATTERN" value="%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{80} - %msg%n"/>
    <property name="LOG_PATH" value="${LOG_PATH:-${LOG_TEMP:-${java.io.tmpdir:/tmp}}}"/>

    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_PATH}/${LOG_FILE}</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_PATH}/${LOG_FILE}.%d{yyyy-MM-dd}</fileNamePattern>
        </rollingPolicy>
        <encoder charset="UTF-8">
            <pattern>${FILE_LOG_PATTERN}</pattern>
        </encoder>

    </appender>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder >
            <pattern>${FILE_LOG_PATTERN}</pattern>
        </encoder>
    </appender>


    <appender name="CRAWLER_LOG" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_PATH}/event.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_PATH}/event.%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>%msg%n</pattern>
        </encoder>
    </appender>


    <logger name="com.business.intelligence.util.CrawlerLogger" level="INFO" additivity="false">
        <appender-ref ref="CRAWLER_LOG"/>

    </logger>
    <root level="INFO">
        <appender-ref ref="STDOUT" />
        <appender-ref ref="FILE"/>
    </root>
</configuration>
```

配置mybaits-config.xml文件

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
<typeAliases>
    <package name="com.sft.ai.model"/>
</typeAliases>
    <mappers>
        <mapper resource ="mapper/mysql.xml"/>
    </mappers>
</configuration>
```





