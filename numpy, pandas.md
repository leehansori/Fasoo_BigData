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

# **numpy vs pandas 성능 비교 건**
   ## **1. Test 확인**
   [Numpy Vs Pandas Performance Comparison] <BR>
   <http://gouthamanbalaraman.com/blog/numpy-vs-pandas-comparison.html#Operations-on-a-Column>

   → 위 테스트에서는 500,000 이상의 row에서 pandas가 numpy보다 더 나은 성능을 발휘한다고 주장 (평균, 로그계산, 중복값 제거 등)

   **TEST** <BR> 

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

## **2. flags를 통해 배열의 속성에 대한 정보를 확인**  

   ```python
   data_rec = data.to_records()
   data_rec.sepal_length.flags
   ``` 
   ![image](https://user-images.githubusercontent.com/109563345/231384119-05ffd27a-e48b-425a-954b-db9f1fff5fda.png)

   1) C_CONTIGUOUS
   - 배열의 데이터가 C 스타일의 행 우선(row-major) 메모리 배치 방식으로 저장되어 있는지를 나타내는 속성
   - True일 경우 배열의 데이터가 메모리에 행이 연속적으로 저장되어 있어 인덱스 계산이 빠름 <br>
      - 행 중심(row-major) 메모리 배치 방식 : 다차원 배열의 요소를 행(row) 단위로 메모리에 저장하는 방식. 한 행의 요소들이 연속적으로 메모리에 저장되고, 다음 행의 요소들이 그 다음 위치에 연속적으로 저장되는 방식으로 c, c++ 에서 일반적으로 사용됨
      <img src="https://user-images.githubusercontent.com/109563345/231621004-8896f889-bf36-4f49-8d70-8bd2c6f84835.png" width="200">
      → 메모리에는 다음과 같이 1차원으로 펴져서 저장된다. [ a11 a12 a13 a21 a22 a23 a31 a32 a33] <br><br>

   2) F_CONTIGUOUS
   - 배열의 데이터가 Fortran 스타일의 열 우선(column-major) 메모리 배치 방식으로 저장되어 있는지를 나타내는 속성
   - True일 경우 배열의 데이터가 메모리에 열이 연속적으로 저장되어 있어 인덱스 계산이 빠름 <br>
      - 열 중심(column-major) 메모리 배치 방식 : 다차원 배열의 요소를 열(column) 단위로 메모리에 저장하는 방식. 한 열의 요소들이 연속적으로 메모리에 저장되고, 다음 열의 요소들이 그 다음 위치에 연속적으로 저장되는 방색으로 Fortran과 같은 언어에서 일반적으로 사용됨
      <img src="https://user-images.githubusercontent.com/109563345/231620757-cf9eca8b-1006-4835-b121-d84d939e39cf.png" width="200">
      → 메모리에는 다음과 같이 1차원으로 퍼져서 저장된다. [ a11 a21 a31 a12 a22 a32 a13 a23 a33 ]

   3) OWNDATA
   - 배열이 소유하는 데이터 메모리를 갖고 있는지 나타내는 속성
   - True일 경우 배열이 자체적으로 데이터를 소유, False일 경우 배열이 데이터를 공유하거나 참조하여 데이터를 소유하지 않음

   **→  데이터를 참조하고 있어 메모리에 연속적으로 저장되어 있지 않음**


## **3. 메모리에 연속적으로 저장하기 위한 방법**  
   ### **3-1. to_numpy() vs to_records()**  
   
   ```python
   data_rec = data.to_records()
   np.mean(data_rec.sepal_length)
   ```
   
   ### **3-2. np.ascontiguousarray**  
   ascontiguousarray 함수는 메모리에 연속적으로 저장되지 않는 배열을 연속적으로 저장되는 배열(row-major. c 스타일)로 변환.
   
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
   print("numpy로 계산한 시간:", end-start)

   # np.ascontiguousarray 적용 후 평균 계산
   np_data = np.ascontiguousarray(data_rec.sepal_length) # 적용
   start = time.time()
   np.mean(np_data)
   end = time.time()
   print("flags['C_CONTIGUOUS'] : ", np_data.flags['C_CONTIGUOUS'])
   print("np.ascontiguousarray로 변환 후 시간:", end-start)

   # pandas로 평균 계산
   start = time.time()
   data.loc[:, 'sepal_length'].mean()
   end = time.time()
   print("\npandas로 계산한 시간:", end-start)

   ```
   **결과** <BR>
  ![image](https://user-images.githubusercontent.com/109563345/231657441-615ee133-349c-4568-83e2-d28e4b380dd8.png)


## **4. Structured Array**
   
