# 开发环境安装部署

---

* 1.1 网络爬虫环境部署

主要是数据内容的爬取

参考爬虫的项目

* 1.2 数据导入

```
####进入localhost页面

品牌数据导入

LOAD CSV WITH HEADERS FROM "file:///bank.csv" AS line

CREATE (:Bank(name:line.bank,count:line))

车系数据导入

LOAD CSV WITH HEADERS FROM "file:///serise.csv" AS line

CREATE (p:Serise(name:line.serise,count:line.count))

品牌/车系关系数据导入

LOAD CSV WITH HEADERS FROM "file:///serise.csv" AS line

MATCH (entity1:Bank{name:line.bank}),(entity2:Serise{name:line.serise})

CREATE (entity1) -[:Subtype{type:line.relation}] ->(entity2)


汽车品牌/车型创建索引

CREATE CONSTRAINT ON (b:Bank)
ASSERT b.name is UNIQUE



```



