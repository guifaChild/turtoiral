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
package com.sft.ai.kg

import org.apache.spark.{SparkConf, SparkContext}
import org.junit.{Before, Test}

class RddTest {
   var sc:SparkContext = _
  @Before
  def initialize():Unit ={
    val conf = new SparkConf()
      .setAppName("First Application")
      .setMaster("local[*]")//本地多线程，指向所有可用的内核
    sc=new SparkContext(conf)
    sc.setLogLevel("OFF")//关闭日志

  }
  @Test
  def test():Unit = {
    val logFile ="C:/SUService.log"
    val logDate = sc.textFile(logFile).cache()
    val numAs= logDate.filter(line=> line.contains("a")).count()
    val numBs= logDate.filter(line=> line.contains("b")).count()
    println("包含a的行有%s,包含b的行有%s",numAs,numBs)

  }
  @Test
  def test2:Unit={
    val data= Array(1,2,3,4,5)
    val listData=sc.parallelize(data)
    println(listData.count())

  }
  @Test
  def testMapRdd() = {
    val input = sc.parallelize(List(1,2,3,4,5))
    val result = input.map(x=>x * x)
    println(result.collect.mkString(","))
  }

  @Test
  def testFlatMap():Unit ={
    val lines =sc.parallelize(List("hello world","hi","you"))
    val words =lines.flatMap(line=>line.split(" "))
    words.collect().foreach(println)
  }

  @Test
  def testFackeCollection():Unit={
    val rdd1=sc.parallelize(List("coffee","coffee","pandas"))
    val rdd2= sc.parallelize(List("coffee","coffee","listslk"))
    //去重
    println(rdd1.distinct().collect().mkString(","))
    //  取合集
    println(rdd1.union(rdd2).collect().mkString(","))
    //取交集
    println(rdd1.intersection(rdd2).collect().mkString(","))

    //取差集
    println(rdd1.subtract(rdd2).collect().mkString(","))

    //取笛卡尔积

    println(rdd1.cartesian(rdd2).collect().mkString(","))
  }



}
```

# 自定义图

---

创建PropertiesGraph的object文件

```
package com.sft.ai.kg

import org.apache.spark.graphx.{Edge, Graph, VertexId}
import org.apache.spark.rdd.RDD
import org.apache.spark.{SparkConf, SparkContext}

object PropertiesGraph {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("graph")
      .setMaster("local[*]")
    val sc = new SparkContext(conf)
    sc.setLogLevel("OFF")
    //定义点
    val users:RDD[(VertexId,(String,String))] = sc.parallelize(
      Array(
        (3L,("hanmeimei","student")),
        (7L,("lilei","postdoc")),
        (5L,("zixun","prof")),
        (2L,("haoran","prof"))
      )
    )
    //定义边
    val relationships :RDD[Edge[String]] = sc.parallelize(
      Array(
        Edge(3L,7L,"cooperate"),
        Edge(5L,7L,"advisor"),
        Edge(2L,5L,"colleague")

      )
    )
    val defaultUser = ("jack ma","defaulUser")
    val graph =Graph(users,relationships,defaultUser)

    //找到图中属性是student的点
    graph.vertices.filter {

    case (id,(name,work)) => work =="student"

    }.collect.foreach{case(id,(name,work)) => println(s"$name is $work")}
//找到图中属性为advisor的边

    graph.edges.filter(x=>x.attr =="advisor").collect.foreach(x=>println(s"${x.srcId} to ${x.dstId} 属性 为${x.attr}" ))

    def max(a:(VertexId,Int),b:(VertexId,Int)) :(VertexId,Int) ={
      if (a._2 >b._2) a else b
    }
    println("最大的出度"+graph.outDegrees.reduce(max))
    println("最大的入度"+graph.inDegrees.reduce(max))
    println("最大的度数"+graph.degrees.reduce(max))

   //给每个点的职业属性上加上一个字符串
    graph.mapVertices{case(id,(name,work))=>(id,(name,work+"spark"))}.vertices.collect()
      .foreach(println)


    //triplet a->b
    //给图中的每个元组的Edge的属性值设置为源的属性值+边的属性值+目标点的属性值

    graph.mapTriplets(x=>x.srcAttr._2+"+"+x.attr+"+"+x.dstAttr._2)
      .edges.collect().foreach(println)

    graph.triplets.map(x=>x.srcAttr._1+"is the " +x.attr+"of " +x.dstAttr).foreach(println)

    //删除节点，构建子图

    val validGraph =graph.subgraph(vpred = (id,attr)=>attr._2 !="postdoc")
    validGraph.vertices.foreach(println)
    validGraph.triplets.map(x=>x.srcAttr._1 +" is the "+x.attr +" of "+x.dstAttr._1)
      .foreach(println)
    //构建职业是professor的子图
    val subGraph = graph.subgraph(vpred = (id,attr)=>attr._2 =="professor")
    subGraph.vertices.collect().foreach(x=>println(s"${x._2._1} is ${x._2._2}"))



  }
}
```

# spark 链接neo4j

---

查看neo4j的conf目录下的neo4j.conf配置中的ip与端口号

dbms.connector.bolt.listen\_address = 0.0.0.0:7687

重启数据库

新建文件SparkGraphConnector的object文件

```
package com.sft.ai.kg

import org.apache.spark.SparkConf
import org.apache.spark.api.java.JavaSparkContext
import org.apache.spark.graphx.Graph
import org.neo4j.driver.internal.InternalNode
import org.neo4j.spark.Neo4j

object SparkGraphxConnector {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf()
      .setAppName("neo4j")
      .setMaster("local[*]")
      .set("spark.neo4j.bolt.url","bolt://192.168.1.49")
      .set("spark.neo4j.bolt.user","neo4j")
      .set("spark.neo4j.bolt.password","123456")

    val sc = new JavaSparkContext(conf)
    sc.setLogLevel("OFF")

    val neo4j = Neo4j(sc)

    val rdd = neo4j.cypher("match (n:Person) return n ").loadRowRdd

    val personRDD = rdd.map(row=>{
      val map =row.get(0).asInstanceOf[InternalNode]
      new Person(map.get("home").asString(),
        map.get("name").asString(),
        map.get("personId").asString()
      )
    })
    personRDD.foreach(println)

    val graphQuery= "match (p:Person) = [r] - (a:Person) return id(p) as source,id(a) as target, type(r) as value"
    val graph:Graph[String,String] = neo4j.rels(graphQuery).loadGraph
    graph.edges.foreach(println(_))

  }
  case class Person(
                   val home:String,
                   val name:String,
                   val personId:String
                   )
}

```



需要把整个工程打包成为jar放到大数据的环境中运行



修改配置文件

```
<build>
    <plugins>
      <plugin>
        <groupId>org.scala-tools</groupId>
        <artifactId>maven-scala-plugin</artifactId>
        <version>2.15.2</version>
        <executions>
          <execution>
            <goals>
              <goal>compile</goal>
              <goal>testCompile</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>1.4</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>

          </execution>
        </executions>
        <configuration>
          <fileters>
            <filter>
              <artifact>*:*</artifact>
              <excludes>
                <exclude>META-INF/*.SF</exclude>
                <exclude>META-INF/*.DAS</exclude>
                <exclude>META-INF/*.RSA</exclude>
              </excludes>
            </filter>
          </fileters>
          <transformers>
            <transformer
                        implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer">
              <resource>META-INF/spring.handlers</resource>
            </transformer>
            <transformer
                    implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
              <mainClass>META-INF/spring.handlers</mainClass>
            </transformer>
            <transformer
                    implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer">
              <resource>META-INF/spring.schemas</resource>
            </transformer>
          </transformers>
        </configuration>
      </plugin>
    </plugins>
  </build>
  
  
```

maven中install打成jar包

寻找target下的目录jar拷贝到大数据环境

spark-submit  --master spark://spark-master:7077 --class com.sft.ai.kg.SparkGraphxConnector /workspace/scala-jar/spark-graphx-engine-1.0.SNAPSHOT.jar































