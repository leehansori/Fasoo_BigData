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
   ### **1. Test 확인**
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

   **결과** <BR>
   ![image](https://user-images.githubusercontent.com/109563345/231366441-8434755c-4fcd-4f7c-903d-c109d2b925f3.png)

   → 실제로 numpy가 약간 오래 걸림
  
   ### **2. flags 속성을 통해 배열의 속성에 대한 정보를 확인**  
   
   ```python
   data_rec.sepal_length.flags
   ``` 
   ![image](https://user-images.githubusercontent.com/109563345/231384119-05ffd27a-e48b-425a-954b-db9f1fff5fda.png)
   
   2-1. C_CONTIGUOUS
   - 배열의 데이터가 C 스타일의 행 우선(row-major) 메모리 배치 방식으로 저장되어 있는지를 나타내는 속성
   - True일 경우 배열의 데이터가 메모리에 연속적으로 저장되어 있어 인덱스 계산이 빠름 <br>
      - 행 중심(row-major) 메모리 배치 방식 : 배열의 각 로우(row)가 연속적으로 저장되어 있고, 열(column)이 연속적으로 변하는 방식으로 데이터가 저장

   2-2. F_CONTIGUOUS
   - 배열의 데이터가 Fortran 스타일의 열 우선(column-major) 메모리 배치 방식으로 저장되어 있는지를 나타내는 속성
   - True일 경우 배열의 데이터가 메모리에 연속적으로 저장되어 있어 인덱스 계산이 빠름 <br>
    *열 중심(column-major) 메모리 배치 방식 : 배열의 각 컬럼이 연속적으로 저장되어 있고, 로우가 연속적으로 변하는 방식으로 데이터가 저장

   2-3. OWNDATA
   - 배열이 소유하는 데이터 메모리를 갖고 있는지 나타내는 속성
   - True일 경우 배열이 자체적으로 데이터를 소유, False일 경우 배열이 데이터를 공유하거나 참조하여 데이터를 소유하지 않음
   
   **→ 메모리에 연속적으로 저장되어 있지 않고, 데이터를 참조하고 있음**
   
   ### **3. to_numpy() vs to_records()**  
   ### **4. Structured Array**
   
