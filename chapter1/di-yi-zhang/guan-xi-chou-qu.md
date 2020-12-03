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

其实就是根据模板抽取，然后出现过这中实体的也归结到这种关系中



* 对于每一种关系，需要提前定好至少一种模板
* 每次迭代都有可能减低准确率
* 不具备概率解释性
* * 可以结合人工检查















