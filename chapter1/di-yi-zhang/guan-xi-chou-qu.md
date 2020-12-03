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





