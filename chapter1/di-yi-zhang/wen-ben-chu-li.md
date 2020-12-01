# 命名实体识别

---

## 命名实体识别技术

### 命名实体识别的重要性

* 需要确定出实体，才能确定关系，实体识别是抽取关系的基础

* 问答系统中同样 知道了实体，才能确定槽位是否填满，进行任务下面的执行。

## 命名实体识别的评价指标

---

* 精确率

* 召回率

* F1

## 基于规则的命名实体识别

---

需要构建词典库存储命名的实体表。

## 命名实体识别-----分类

---

* 分类 ------是否是实体名称   需要抽取特征、当前词、前面词、后面词等等

* 逻辑回归、支持向量机、最大熵模型--------时序无关的模型

* 条件随机场、隐式马科夫模型、递归神经网络--------时序有关的模型

## 特征提取

---

* 基于单词的特性：当前词语、前后词、前前后后词

* stemming

* 词性：当前词语、前后词、前前后后词的词性

* 单词的前缀/后缀：当前词、前后词、。。。

* 当前词的特点：长度、包含大小写、多少个数字、多少大写字母、多少数字、“-”、

* 句法/依存

## 变量处理

---

* 连续变量离散化处理

* 归一化

* 类型进行独热编码处理

* 等等。。。。

## 命名实体识别（1）——Baseline

---

1、样本样例   sentence：word、pos（词性）、Tag（type）

2、code 基于统计的方法

```py
"""
Majoroity Voting
1、统计一个单词最有可能被分成的实体类别
2、给定一个新的单词的时候，把它分类成这个类别
London：B-geo London ：B-geo London：O London：O   London： B-geo
未来，如果在见到London这个单词，这个时候把他分类为B-geo

"""
import  pandas as pd
import numpy as np

data=pd.read_csv("sample.csv",encoding='utf-8')
# 对Na做一个简单的处理,向后填充
data.fillna(method="ffill")
data.tail(10)
words =list(set(data['Word'].values))
n_words=len(words)

from sklearn.base import BaseEstimator,TransformerMixin
class MajorityVotingTagger(BaseEstimator,TransformerMixin):
    def fit(self,X,y):
        """
        X:list of words
        y:list of tags(实体类别)

        """
        word2cnt={}
        self.tag=[]
        for x,t in zip(X,y):
            if t not in self.tags:
                self.tag.append(t)
            if x in word2cnt:
                if t in word2cnt[x]:
                    word2cnt[x][t] +=1
                else:
                    word2cnt[x][t]=1
            else:
                word2cnt[x]={t:1}
        self.mjvot={}
        for k,d in word2cnt.items():
            self.mjvot[k]=max(d,key=d.get)


    def predict(self,X,y):
        return [self.mjvot.get(x,'O') for x in X]

#
words = data["word"].values.tolist()
tags=data["Tag"].values.tolist()

from sklearn.model_selection import cross_val_predict
from sklearn.metrics import classification_report
pred=cross_val_predict(estimator=MajorityVotingTagger(),X=words,y=tags,c=5)
report =classification_report(y_pred=pred,y_true=tags)
print(report)
```

## 命名实体识别---方法2

---

基于随机森林的方法构建分类器

code

```py
import  pandas as pd
import numpy as np

data=pd.read_csv("sample.csv",encoding='utf-8')
# 对Na做一个简单的处理,向后填充
data.fillna(method="ffill")
data.tail(10)
words =list(set(data['Word'].values))
n_words=len(words)
def get_feature(word):
    return np.array([word.istitle(),word.islower(),word.isupper(),len(word),word.isdigit()
                     ,word.isalpha()])

words=[get_feature(w) for w in data["word"].values.tolist()]
tags =data["Tag"].values.tolist()
from sklearn.model_selection import cross_val_predict
from sklearn.metrics import classification_report
from sklearn.ensemble import RandomForestClassifier
pre=cross_val_predict(RandomForestClassifier(n_estimators=20),X=words,y=tags,cv=5)
report =classification_report(y_pred=pred,y_true=tags)
print(report)
```

## 命名实体识别--------加入特征工程

---

加入前后词的特征，是否是句子的首单词，是否是句子的尾部单词

```py
def get_sentences(data):
    agg_func=lambda s:[(w,p,t) for w,p,t in zip(s["word"].values.tolist()
                                                ,s["pos"].values.tolist(),
                                                 s["tag"].values.tolist())]

    sentence_grouped=data.groupby("Sentence #").apply(agg_func)
    return [s for s in sentence_grouped]
sentences=get_sentences(data)
from sklearn.preprocessing import LabelEncoder

out=[]
y=[]
mv_tagger=MajorityVotingTagger()
tag_encoder=LabelEncoder()
pos_encoder=LabelEncoder()
words=data["word"].values.tolist()
tags=data["tags"].values.tolist()
pos=data["pos"].values.tolist()
mv_tagger.fit(words,tags)
tag_encoder.fit(tags)
pos_encoder.fit(pos)

for sentence in sentences:
    for i in range(len(sentences)):
        w,p,t=sentence[i][0],sentence[i][1],sentence[i][2]

        if i<len(sentence)-1 :
            # 如果不是句子中的最后一个单词，则可以提取出下文的特征
            mem_tag_r = tag_encoder.transform(mv_tagger.predict(sentence[i+1][0]))[0]
            true_pos_r = pos_encoder.transform([sentence[i+1][0]])[0]
        else:
            mem_tag_r=tag_encoder.transform(['O'])[0]
            true_pos_r=pos_encoder.transform(['.'])[0]
        if i>0:
#            如果不是句子的第一个单词，则可以提取上问特征
            mem_tag_l=tag_encoder.transform(mv_tagger.predict(sentence[i-1][0]))[0]
            true_pos_l=pos_encoder.transform([sentence[i-1][0]])[0]
        else:
            mem_tag_l = tag_encoder.transform(['O'])[0]
            true_pos_l = pos_encoder.transform(['.'])[0]

        out.append(np.array([w.istitle(),w.islower(),w.isupper(),len(w),
                 w.isdigit(),w.isalpha(),tag_encoder.transform(mv_tagger.predict([w]))[0],
                  pos_encoder.transform([p])[0],mem_tag_r,true_pos_r,mem_tag_l,true_pos_l
                  ]))
        y.append(t)
from sklearn.model_selection import cross_val_predict
from sklearn.metrics import classification_report
from sklearn.ensemble import RandomForestClassifier
pre=cross_val_predict(RandomForestClassifier(n_estimators=20),X=out,y=y,cv=5)
report =classification_report(y_pred=pred,y_true=y)
print(report)
```

## 命名实体识别 -------基于CRF

---



```py
def word2feature (sent,i):
    """
    sent:input sentence
    i:index of word
    """
    word = sent[i][0]
    postag=sent[i][1]
    features={
        'bias': 1.0,
        'word.lower':word.lower(),
        'word[-3:]':word[-3:],
        'word[-2:]':word[-2:],
        'word.isupper()':word.isupper(),
        'word.istitle()':word.istitle(),
        'word.isdigit()':word.isdigit(),
        'postag':postag,
        'postag[:2]': postag[:2],
    }

    if i > 0 :
        word1 =sent[i-1][0]
        postag1=sent[i-1][1]
        features.update(
            {
                '-1:word.isupper()': word1.isupper(),
                '-1:word.istitle()': word1.istitle(),
                '-1:word.isdigit()': word1.isdigit(),
                '-1:postag': postag1,
                '-1:postag[:2]': postag1[:2],
            }
        )
    else:
        features['BOS'] = True

    if i<len(sent) -1 :
        word1 = sent[i +1][0]
        postag1 = sent[i + 1][1]
        features.update({
                '+1:word.isupper()': word1.isupper(),
                '+1:word.istitle()': word1.istitle(),
                '+1:word.isdigit()': word1.isdigit(),
                '+1:postag': postag1,
                '+1:postag[:2]': postag1[:2],})
    else:
        features['EOS']=True

def sent2feature(sent):
    return [word2feature(sent,i) for i in range(len(sent))]

def sent2labels(sent):
    return [sent for token,postag,label in sent]

X =[sent2feature(s) for s in sentences ]
y =[ sent2labels(s) for s in sentences ]

from sklearn_crfsuite import CRF

crf = CRF(algorithm='lbfgs',c1=0.1,c2=0.1,max_iterations=100)

from sklearn.model_selection import cross_val_predict
from sklearn_crfsuite.metrics import flat_classification_report
from sklearn.ensemble import RandomForestClassifier
pre=cross_val_predict(estimator=crf,X=X,y=y,cv=5)
report =flat_classification_report(y_pred=pred,y_true=y)
print(report)
```











