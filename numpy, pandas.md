---
layout: post
title: "numpy, pandas"
등록일: 2023-04-03
수정일: 2023-04-12
author: 이한솔
---

## **목차**
1. Numpy 메모리 레이아웃
2. numpy vs pandas 성능 비교 건

<Br>
   
---

## **numpy vs pandas 성능 비교 건**
### **<Test 확인>**
[Numpy Vs Pandas Performance Comparison] <BR>
<http://gouthamanbalaraman.com/blog/numpy-vs-pandas-comparison.html#Operations-on-a-Column>
   
→ 위 테스트에서는 500,000 이상의 row에서 pandas가 numpy보다 더 나은 성능을 발휘한다고 주장 (평균, 로그계산, 중복값 제거 등)

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
start = time.time()
np.mean(data_rec.sepal_length)
end = time.time()
print("numpy:", end-start)

# pandas로 평균 계산
start = time.time()
data.loc[:, 'sepal_length'].mean()
end = time.time()
print("pandas:", end-start)

```

**<결과>** <BR>
![image](https://user-images.githubusercontent.com/109563345/231362278-38d93fcf-9654-4ef5-bcd9-bc488f2987d3.png)
   
→ 실제로 numpy가 약간 오래 걸림
   
### **<flags 속성을 통해 배열의 속성에 대한 정보를 확인>**  
 
   
