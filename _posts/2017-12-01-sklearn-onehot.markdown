---
layout: post
title:  "sklearn 学习笔记之 OneHotEncoder"
date:   2017-12-01 15:26:00 +0800
categories: sklearn
---

## one-hot encoding 简单介绍
具体文档可以通过友好的help函数查看：
```python
from sklearn.preprocessing import OneHotEncoder
help(OneHotEncoder)
```
### 官方Examples
 |  Given a dataset with three features and four samples, we let the encoder
 |  find the maximum value per feature and transform the data to a binary
 |  one-hot encoding.
 |  
 |  >>> from sklearn.preprocessing import OneHotEncoder
 |  >>> enc = OneHotEncoder()
 |  >>> enc.fit([[0, 0, 3], [1, 1, 0], [0, 2, 1], [1, 0, 2]])  # doctest: +ELLIPSIS
 |  OneHotEncoder(categorical_features='all', dtype=<... 'numpy.float64'>,
 |         handle_unknown='error', n_values='auto', sparse=True)
 |  >>> enc.n_values_
 |  array([2, 3, 4])
 |  >>> enc.feature_indices_
 |  array([0, 2, 5, 9])
 |  >>> enc.transform([[0, 1, 1]]).toarray()
 |  array([[ 1.,  0.,  0.,  1.,  0.,  0.,  1.,  0.,  0.]])
 |

### 针对pandas的实例
```python
items.head()
```
id  |item_nbr| family|  class|  perishable| family_id
 ---------|------|----|---|------|----
0   |96995  |GROCERY I  |1093   |0  |0
1   |99197  |GROCERY I  |1067   |0  |0
2   |103501 |CLEANING   |3008   |0 |2
3   |103520 |GROCERY I  |1028   |0  |0
4   |103665 |BREAD/BAKERY   |2712   |1  |6

**其中**
```python
len(items['class'].value_counts()) #337
len(items.family.value_counts()) #33
```

由于family是**string**类型，而OneHotEncoder的帮助文档中有
*Encode categorical integer features using a one-hot aka one-of-K scheme.*
也就是sklearn只接受int类型特征的one_hot,遂先将family这一行映射到int，具体做法可以：
```python
d = dict(zip(family_index,range(len(items.family.value_counts().index))))
items['family_id'] = items.apply(lambda row:d[row['family']],axis=1)
```

**enc.fit()也接受直接传入一个dataframe作为参数的，会对所传dataframe的所有列进行one_hot**
**注意：** 如果对一列进行one_hot:传入的必须是`items[['family_id']]` 而不是 `items['family_id']`，因为前者类型是一个**DataFrame**,而后者是**Series**，当然，如果你非要传入后者，可以进行`reshape(len(items),1) `哈哈
  针对本例中的item：
```python
  enc.fit(items[['family_id']])
  enc.fit(items[['family_id','class']])
  enc.transform(items[['family_id']]).toarray() 
  enc.transform(items[['family_id','class']]).toarray() 
```
或者直接跟原DataFrame合并：
`pd.concat([items,pd.DataFrame(enc.transform(items[['family_id','class']]).toarray())],axis=1)`





