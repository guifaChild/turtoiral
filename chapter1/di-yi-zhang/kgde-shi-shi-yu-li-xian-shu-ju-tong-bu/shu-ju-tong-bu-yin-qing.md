# 数据同步引擎搭建

---

1、新建maven项目

2、配置pom文件

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.sft.ai.data.engine</groupId>
    <artifactId>datasyncengine</artifactId>
    <version>1.0-SNAPSHOT</version>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>8</source>
                    <target>8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <dependencies>
        <dependency>
            <groupId>org.neo4j.driver</groupId>
            <artifactId>neo4j-java-driver</artifactId>
            <version>1.6.3</version>
        </dependency>
        <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>2.0.0</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.0</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.dataformat</groupId>
            <artifactId>jackson-dataformat-yaml</artifactId>
            <version>2.9.0</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.16.16</version>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.7</version>
        </dependency>
    </dependencies>
</project>
```

2、配置application.yml文件

```
neo4jDatasource:
  uri: bolt://192.168.1.49:7687
  username: neo4j
  password: 123456

kafkaConsumer:
  bootstrap.servers: 192.168.1.7:9092
  group.id: test
  enable.auto.commit: true
  auto.commit.interval.ms: 1000
  key.deserializer: org.apache.kafka.common.serialization.StringDeserializer
  value.deserializer: org.apache.kafka.common.serialization.StringDeserializer

topic: example
kafkaPartitions: 1

```

3、配置解析文件类YamlParser

```
package com.sft.ai.config;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.dataformat.yaml.YAMLFactory;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.exception.ExceptionUtils;

import java.io.IOException;
import java.io.Serializable;
import java.net.URL;
@Slf4j
public class YamlParser implements Serializable {
    private static ObjectMapper mapper = new ObjectMapper(new YAMLFactory());
    public static <T> T readValue (String yamlFilePath,Class<T> clazz){
        URL resource =ClassLoader.getSystemClassLoader().getResource(yamlFilePath);
        if (resource == null ){
            log.error("yarml config file not found path is {}",yamlFilePath);
            throw new RuntimeException("config file not found path is {}");
        }
        try {
            return mapper.readValue(resource,clazz);
        } catch (IOException e) {
            log.error("exception is {}", ExceptionUtils.getStackTrace(e));
            throw  new RuntimeException(e);
        }


    }
}

```

4、配置文件的配置类YamlConfig.java

```
package com.sft.ai.config;

import lombok.Data;

import java.util.Iterator;
import java.util.Map;
import java.util.Properties;
import java.util.Set;

@Data
public class YamlConfig {
    private Map<String,String> neo4jDatasource;
    private Map<String,String> kafkaConsumer;
    private String topic;
    private int kafkaPartitions;

    private Properties kafkaConsumerProperties = new Properties();
    public Properties getKafkaConsumerProperties(){
        Set kafkaConsumerSet= kafkaConsumer.keySet();
        Iterator<String> kafkaConsumSetIterator = kafkaConsumerSet.iterator();
        while (kafkaConsumSetIterator.hasNext()){
            String key =kafkaConsumSetIterator.next();
            String value =kafkaConsumer.get(key);
            kafkaConsumerProperties.put(key,value);

        }
        return kafkaConsumerProperties;
    }

}

```

5、编写消费者代码多线程进行消费  ReceiveThread.java

```
package com.sft.ai.controller;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;

import java.time.Duration;
import java.util.Arrays;
import java.util.Properties;

public class ReceiveThread implements Runnable{

    private KafkaConsumer<String,String> consumer;
    public ReceiveThread(Properties properties,String topic){
        consumer =new KafkaConsumer<String, String>(properties);
        consumer.subscribe(Arrays.asList(topic));
    }
    public void run() {
        try{
        while (!Thread.currentThread().isInterrupted()) {
            ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(5));
            for (ConsumerRecord record : records) {
                if (record != null) {
                    handing(record);
                }
            }

        }
    }finally{
        consumer.close();
        }
    }

    private void handing(ConsumerRecord record) {
        System.out.println(Thread.currentThread().getName()+record.value());
        /**
         *
         */
        //if record to json  把record转换成json格式
        //获取到表明
        //json.get table
        //if tablename == user
        //原来的数据，新的数据
        //构建neo4j的关系图


    }
}

```

6、编写程序入口类application类，进行启动

```
package com.sft.ai;

import com.fasterxml.jackson.dataformat.yaml.YAMLParser;
import com.sft.ai.config.YamlConfig;
import com.sft.ai.config.YamlParser;
import com.sft.ai.controller.ReceiveThread;

import java.util.Properties;
import java.util.concurrent.Executor;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Application {


    public static void main(String[] args) {
        YamlConfig config = YamlParser.readValue("application.yml",YamlConfig.class);
        System.out.println(config.getTopic());
        System.out.println(config.getKafkaPartitions());
        //用多线程的方式来进行消费者的启动，用partitions来控制线程数
        int kafkaPartitions =config.getKafkaPartitions();
        ExecutorService executorService = Executors.newFixedThreadPool(kafkaPartitions);

        Properties properties=config.getKafkaConsumerProperties();
        ReceiveThread thread =new ReceiveThread(properties, config.getTopic());
        for(int i =0;i <kafkaPartitions;i++){
            executorService.execute(thread);
        }
    }
}

```

7、启动服务

8、更改mysql进行验证实验。

