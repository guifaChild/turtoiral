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







```



