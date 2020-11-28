# neo4j的安装

---

##### neo4j的下载

* 文件下载

```
下载网址社区版，注意检查jdk版本 http://neo4j.com/
```

* neo4j配置文件

```
主要配置neo4j.conf文件，可以修改默认的数据库、端口号、ip地址等，在$neo4j的conf目录下
```

* neo4j启动

```
启动命令bin/neo4j 直接启动
```

* 数据的导入

```
neo4j-admin import -node data.csv -node data2.csv -relationships data_data2.csv 默认的数据库是graph.db数据库
```

### 



