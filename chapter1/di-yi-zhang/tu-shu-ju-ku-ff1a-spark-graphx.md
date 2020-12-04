# 图数据库：Spark GraphX

---

# spark-shell 操作命名

---

在安装目录下输入：

```
bin/spark-shell
```

```
//创建rdd
val textFile =sc.textFile("conf/slaves.template")
```

```
//查看条数
textFile.count()
//查看第一行
textFile.first()

//包含的个数
textFile.filter(line=>line.contains("the")).count()

//每行最多单词的数量是14
textFile.map(line=>line.split(" ").size).reduce((a,b)=>if（a>b） a else b)

// 统计单词的数量
val wordCounts=textFile.flatMap(line=>line.split(" ")).map(word=>(word,1)).reduceByKey((a,b)=>a+b)
```

# spark -rdd的操作

---

创建新的工程

```
勾选 Create from archetype
```

修改配置项：

```
scala版本修改为2.11.12
```

App修改

```
Application修改为App
```

删除相对应的测试文件

```
AppTest
MySpec
```

正常输出hello word

配置文件新增

```
  <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-sql_2.11</artifactId>
      <version>2.4.0</version>
    </dependency>
    <dependency>
      <groupId>com.thoughtworks.paranamer</groupId>
      <artifactId>paranamer</artifactId>
      <version>2.8</version>
    </dependency>
    <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-graphx_2.11</artifactId>
      <version>2.4.0</version>
    </dependency>
    <dependency>
      <groupId>neo4j-contrib</groupId>
      <artifactId>neo4j-spark-connector</artifactId>
      <version>2.1.0-M4</version>
    </dependency>
```

源中配置

```
    <repository>
      <id>SparkPackagesRepo</id>
      <url>http://dl.bintray.com/spark-packages/maven</url>
    </repository>
```

创建RddTest class文件

```

```



