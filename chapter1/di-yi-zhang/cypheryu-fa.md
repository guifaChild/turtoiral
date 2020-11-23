# Cypher语法

---

## Neo4j中的SQL

1. match 相当于SQL 中的select

```py
match
     (node) - [relationship] -> (node)
where 
     (node | relationship)
return 
     (node | relationship)

##查询一个实体通过一种关系，关联到另一个实体
```

1.1 简单样例

```py
match (n:Pserson) return n limit 10 
## limit 与sql中的limit意义相同
```

1.2 trick

```Cypher
选中节点，下面展示节点的不同属性
选中上面的名称 可以选择节点展示内容和颜色
双击节点展示所有的关联节点
```

1.3 带条件样例

```py
match (n:Person) -[:HAS_PHONE] ->(p:Phone) return n,p limit 10
##没有limit会都进行返回 
## 有关系的查询
match (n:Person) -[:HAS_PHONE] ->(p:Phone) where n.name="姓名6" return n,p limit 10

```



