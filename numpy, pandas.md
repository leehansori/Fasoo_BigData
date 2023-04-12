---
layout: post
title: "numpy, pandas"
등록일: 2023-04-03
수정일: 2023-04-12
author: 이한솔
---

## **목차**
1. 데이터셋

<Br>
   
---

## **numpy vs pandas 성능 비교 건**
[Numpy Vs Pandas Performance Comparison] <http://gouthamanbalaraman.com/blog/numpy-vs-pandas-comparison.html#Operations-on-a-Column>
   
→ 위 테스트에서는 500,000 이상의 row에서 pandas가 numpy보다 더 나은 성능을 발휘한다고 주장

```python
import pandas as pd
import seaborn as sns
import numpy as np
import time
import sys

# 데이터 500만개 생성해서 dataframe, ndarray로 변환
iris = sns.load_dataset('iris')
data = pd.concat([iris]*333334)
data_rec = data.to_records()
print (len(data), len(data_rec))
   
# numpy로 평균 계산
np_data = data_rec.sepal_length
start = time.time()
np.mean(np_data)
end = time.time()
print("numpy:", end-start)

# pandas로 평균 계산
pandas_data = data.loc[:, 'sepal_length']
start = time.time()
pandas_data.mean()
end = time.time()
print("pandas:", end-start)

```

<결과> <BR>
![image](https://user-images.githubusercontent.com/109563345/231362278-38d93fcf-9654-4ef5-bcd9-bc488f2987d3.png)
