# 的觐见的进件的规则的存储

---

## 规则引擎理解

由于业务来创建查询语句，程序对数据库进行查询

![](/assets/test.png)

---

## 规则引擎的开发

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



