# 非结构化数据处理

---

## 1、常用算法

![](/assets/kar19.png)

非结构化的数据通常指的是文本，百科、邮箱

首先需要进行信息抽取如人名、地名、机构名等，常用的技术是命名实体的识别和关系的抽取，在这个过程中会用到几个关键的技术。实体消歧、链接预测

# 文本分析几个关键的技术

---

* 拼写纠错（Spell Correction） \(文本分析系统需要，如百度、谷歌等现在的电商系统也有\)

* 分词 （Word Segementation ）

* 词干提取 （Stemming ）

* 词的过滤 （Filtering Words）

* 文本的表示 （Text Representation）

* 文本的相似度 （Text Similarity）

* 词向量 （Word2Vec）

* 句子向量（Sentence2Vec）

* 实体命名识别 （Name Entity Recognition）

## 拼写纠错

---

拼写纠错

找出编辑距离最近的单词（从一个单词变到另一个单词需要的最少操作数）  算法实现  思考实现方式  （采用DP的方式实现）

```py
#编辑距离的计算  主要分为三种操作 增加 删除 更改

def edit_dist(str1,str2):
    m,n=len(str1),len(str2)
    dp=[[0 for i in range(n+1)] for i in range(m+1)]
    for i in range(m+1):
        for j in range(n+1):
#             假设第一个字符串为空
            if i==0 :
                dp[i][j] =j

            elif j==0 :
                dp[i][j]==i
            #     以上为初始化的内容
            elif str1[i-1] == str2[j-1] :
                dp[i][j]=dp[i-1][j-1]
            else :
                dp[i][j] = 1+min(dp[i-1][j],dp[i][j-1],dp[i-1][j-1])
    return dp[m][n]




```























