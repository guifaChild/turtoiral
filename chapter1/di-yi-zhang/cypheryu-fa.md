# Cypher语法

---

## Neo4j中的SQL

* match 相当于SQL 中的select

```py
match
     (node) - [relationship] -> (node)
where 
     (node | relationship)
return 
     (node | relationship)

##查询一个实体通过一种关系，关联到另一个实体
```

* 简单样例

```py
match (n:Pserson) return n limit 10 
## limit 与sql中的limit意义相同
```

* trick

```Cypher
选中节点，下面展示节点的不同属性
选中上面的名称 可以选择节点展示内容和颜色
双击节点展示所有的关联节点
```

* 带条件样例

```py
match (n:Person) -[:HAS_PHONE] ->(p:Phone) return n,p limit 10
##没有limit会都进行返回 
## 有关系的查询
match (n:Person) -[:HAS_PHONE] ->(p:Phone) where n.name="姓名6" return n,p limit 10
```

## 多维度查询

* 几度关系查询

```
match (n:Person) -[:HAS_PHONE]->(p:Phone)-[:CALL]-(p1:Phone) where n.name="姓名6" return n,p,p1 limit 10
查询的是两度关系内的数据
```

* 利用关系查询 \(不限定实体，只限定关系\)

```
match p=()-[a:CALL]-() return p limit 10
```

* 利用正则查询

```
match (n:USER) where n.name=~'Jack.*' return n limit 10
##其实就是利用正则进行一些查询
```

* 包含查询  \(contains\)

```
match (n:USER) where n.name= contains 'Jack' return n limit 10
##指定包含的字符串
```

## 创建实体关系

* 创建节点与关系

```
create (n:Person) -[:Loves] ->(m:Dogs)
##创建两个节点和一个关系
```

* 创建带有属性的关系和实体

```
create (n:Person{name:"李四"}) -[:FEAR{level:1}] ->(t:Tiger{type:"东北虎"})
##创建的关系和实体
```

* 给两个没有关系的实体创建关系

```
create (n:Person{name:"张三"})
create (n:Person{name:"李四"})

match (n:Person{name:"张三"}),(m:Person{name:"李四"}) return n,m  ##先进行查询验证


match (n:Person{name:"张三"}),(m:Person{name:"李四"}) create n-[k:KNOW]->[m] return k
```

* merge 有则返回，没有则进行创建

```
match (n:Person{name:"张三"}),(m:Person{name:"李四"}) merge n-[L:love]->[m] return k
```

## 删除实体关系

* 删除两个实体之间的关系

```
match (n:Person{name:"李四"})-[f:FEAR] ->(m:Tiger) delete f
match (n:Person{name:"李四"})-[f:FEAR] ->(m:Tiger) return n ##验证查询
```

* 删除实体

```
match (n:Person{name:"李四"}) delete n
```

删除实体之前先删除关系

* 同时 删除关系和实体

```
match (n:Person) -[l:Loves] ->(d:Dog) delete n,l,d
```

# 更新实体关系

* 更新实体

```
match (t:tiger) where id(t) =1817 return t  ## 查询验证
match (t:tiger) where id(t) =1817 set t:A return t  ## 加上了一个标签，一个实体可以有多个标签

```

标签传播算法























