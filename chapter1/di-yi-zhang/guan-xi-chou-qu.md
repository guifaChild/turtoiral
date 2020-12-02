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

```
corpus text
Bill Gates founded Microsoft in 1975
```



                                                                                                  Training data

                                                                                                 `  (Bill Gates ,Microsoft)  Label:Founder Feature:X founded Y`

```
Freebase
Founder：(Bill Gates,Microsoft)
```

负样本：

对数据进行采用

