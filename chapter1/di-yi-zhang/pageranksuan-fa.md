# PageRank算法

---

佩奇排序算法

对搜索结果进行排序

## pagerank基本思想：

---

根据节点的入度与出度计算节点的重要性：

重要性$$PR(T)/L(T)$$，$$PR(T)$$表示T的pagerank值，L\(T\)代表T的出链数。A的PR值是针对这些所有值的累加。

## pagerank的基本概念：

---

出链    A网页中有对B的链接  A-&gt;B

入链     由A入链B

无出链   没有任何的出链

只对自己出链  只指向自己的链接

PR值  这个网页被访问的概率，重要性等

## pagerank的出入链关系：

---

A的pr值：对没有出链的进行假设对所有的网页都有出链

随机浏览者概念，1/N ,残差概率（阻尼系数）0.85代表用户访问到某个页面后，继续访问下个页面的概率。（1-d）停止点击，随即浏览，为了处理没有外部浏览的情况。

网页即是节点，出入链表示关系，PR值在这里表示的是节点的影响重要度。

## 马尔可夫链

---

随机漫步：

当前的状态只与上一状态有关，

收敛定理：

1、可能的状态数是有限的

2、转移概率的值是固定不变的

3、从任意状态能转移到任意其他值

4、不是简单的循环。

```py
import numpy as np
def markov():
    init_array=np.array([0.3,0.2,0.4,0.1])
    transfer_matrix=np.array([[0,0.5,0,0],
                              [0.333, 0, 0, 0.5],
                              [0.333, 0, 1, 0.5],
                              [0.333, 0.5, 0, 0],
                              ])
    restmp=init_array
    for i in range(100):
        res =np.dot(restmp,transfer_matrix)
        print(i,'======',res)
        restmp=res

if __name__ == '__main__':

    markov()
```

代码进行改进加入阻尼系数：

```py
import numpy as np
def markov():
    init_array=np.array([0.3,0.2,0.4,0.1])
    transfer_matrix=np.array([[0,0.5,0,0],
                              [0.333, 0, 0, 0.5],
                              [0.333, 0, 1, 0.5],
                              [0.333, 0.5, 0, 0],
                              ])
    restmp=init_array
    for i in range(100):
        res =0.85 * np.dot(restmp,transfer_matrix) + 0.15 *restmp
        print(i,'======',res)
        restmp=res

if __name__ == '__main__':

    markov()
```

## GraphX来实现

---

```
create (p:Page{value:"A"});
create (p:Page{value:"B"});
create (p:Page{value:"C"});
create (p:Page{value:"D"});
match (a:Page{value:"A"}),(b:Page{value:"B"})  create (a) -[r:TO] ->(b) return r;
match (a:Page{value:"A"}),(b:Page{value:"B"})  create (b) -[r:TO] ->(a) return r;
match (a:Page{value:"A"}),(d:Page{value:"D"})  create (a) -[r:TO] ->(d) return r;
match (a:Page{value:"A"}),(c:Page{value:"C"})  create (a) -[r:TO] ->(c) return r;
match (a:Page{value:"D"}),(b:Page{value:"B"})  create (a) -[r:TO] ->(b) return r;
match (a:Page{value:"B"}),(b:Page{value:"D"})  create (a) -[r:TO] ->(b) return r;
match (a:Page{value:"D"}),(b:Page{value:"C"}) create (a) -[r:TO] ->(b) return r;
```



code

```
package com.sft.ai.kg

import org.apache.spark.SparkConf
import org.apache.spark.api.java.JavaSparkContext
import org.neo4j.spark.Neo4j

object PageRank {
  def main(args: Array[String]): Unit = {
    val conf  = new SparkConf()
      .setAppName("neo4j")
      .setMaster("local[*]")
      .set("spark.neo4j.bolt.url","bolt://192.168.1.49")
      .set("spark.neo4j.bolt.user","neo4j")
      .set("spark.neo4j.bolt.password","123456")
    val sc =new JavaSparkContext(conf)
    sc.setLogLevel("OFF")
    val neo4j = Neo4j(sc)
    val graphFrame =neo4j.pattern(("Page","value"),("TO",null),("Page","value"))
      .partitions(partitions = 1).rows(rows = 100).loadGraphFrame
    val pageRankFrame =graphFrame.pageRank.maxIter(100).run()
    val ranked =pageRankFrame.vertices
    ranked.foreach(println(_))
    val pageRankSort = ranked.orderBy(ranked.col("pagerank").desc).take(3)
    pageRankSort.foreach(println(_))
  }
}

```



