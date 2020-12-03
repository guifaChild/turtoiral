# 关系抽取技术

---

## 远程监督（ Distant Supervision）

1、收集数据

```
文本资料
           ------》training data
freebase
```

正样本：

    corpus text
    Bill Gates founded Microsoft in 1975            
                                                               Training data
                                                               (Bill Gates ,Microsoft) Label:Founder Feature:X founded Y`

    Freebase
    Founder：(Bill Gates,Microsoft)                                                           

负样本：

对数据进行采样

# 关系抽取

---

### 关系抽取的几个常见方法

* 基于自定义模板
* Bootstrap方法
* 监督学习方法
* 无监督学习方法

## 也可以分为：

* 基于规则
* 监督学习
* 半监督 & 无监督学习
* > Bootstrap  
  >  Distant Supervision  
  > 无监督学习

# 基于自定义模板的方法

---

优点就是简单

缺点就是针对没中关系都要自定义模板

* 不容易写出来，不容易维护
* 数千万级的模板
* 可扩展性弱

# 基于规则的方法

---

需要定义Patterns：匹配公式

进行分类：考虑实体类型、考虑实体类型

# 基于监督学习的关系挖掘

---

准备条件：

定义好所有的实体类型、定义好所有的关系类型、标注好的语料库

确定实体、实体间的文本特征、长度、个数、bigram、是否含有某些重要单词等

# Bootstrap方法

---

设计好了有效的模板

大量没有标记过的数据

Bootstrap方法可以看作是半监督学习

其实就是根据模板抽取，然后出现过这种类实体的中间新词也归结到这种关系中作为新的模板进行迭代

* 对于每一种关系，需要提前定好至少一种模板
* 每次迭代都有可能减低准确率
* 不具备概率解释性
* * 可以结合人工检查

## 缺点：

* 对于seed tuples比较敏感
* 精确率低
* 无法评估pattern/tuple
* 错误的叠加

# Snowball

---

（left）\[ORG \]（middle）\[LOC\] \(right\)

$$p=<L_1,T_1,M_1,T_2,R_1>$$

$$s=<L_2,T_1',M_2,T_2',R_2>$$

确定实体是否相等  不等则为O

相等则计算 两两之间的内积

# Snowball的步骤

---

## step1：模板生成

把提取的所有的模板做一个聚类，取平均，降低了不确定性。

怎么进行聚类：

* 从p1到p100开始聚类
* 根据相似度来进行聚类

## step2：记录添加

根据模板循环文本

计算相似度，选择相似度最大的值，把阈值内的添加到seed tuple中

## step3：模板评估

评估模板的可靠性

需要评估每一个模板

根据该模板对记录进行抽取，抽取完之后得到一个记录，

这样可以计算出模板的精确率、召回率



## step4：记录评估

越可靠的记录是由可靠的pattern来生成的

利用confiedence来计算















