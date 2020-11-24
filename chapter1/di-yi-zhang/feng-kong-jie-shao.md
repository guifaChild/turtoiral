# 风控算法介绍

---

## 风控算法建模

![](/assets/import.png)

搭建风控算法的流程，是在已经搭建好的kg基础上进行的，接下来需要做的是特征工程，算法选择。

根据知识图谱抽取一些有效的信息或者特征，然后把所有的特征做成一个向量，通过机器学习进行建模。

### 特征工程

* 申请人相关特征

  ```
  年龄、收入、工作性质等等
  ```

* 从知识图谱提取出的特征

  ```
         从规则提取出来的特征

                    申请人是不是第一次借款（0：不是，1：是）

                    申请人的朋友之前有没有逾期过（0：不是，1：是）

        直接提取出来的特征

                   申请人的二度关系中有多少个节点触碰到了黑名单？（比如3个）

                   申请人的电话记录里晚上12点到凌晨2点的记录的比例是多少（比如0.1）
  ```

* 模型

```
逻辑回归
GBDT
SVM
神经网络
```

## 风控算法评估

模型的评估

```
准确率
AUC
计算一个模型的KS值
```




















