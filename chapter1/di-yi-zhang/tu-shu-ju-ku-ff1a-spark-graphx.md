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









