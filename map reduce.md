---
layout: post
title: "map reduce"
등록일: 2023-04-13
수정일: 2023-04-13
author: 이한솔
---

## **목차**
1. MapReduce Flow
2. Total Order Sorting


---

   > MapReduce는 클러스터 환경에서 대용량의 데이터 처리를 위해 Map, Reduce 두 개의 함수로 처리 과정을 나눈 병렬 처리 기법
   - 오픈 소스
      - Hadoop (분산 처리-MapReduce, 분산 저장-HDFS)
   - Task 예시
      - Word Count : 각 단어의 횟수를 카운팅
      - Distributed Grep : 특정 패턴을 가진 텍스트 검색
      - Reverse Web-Link Graph : 웹 페이지 간의 링크 관계를 그래프로 표현
      - inverted index : 각 단어가 어떤 문서에 나타나는지를 기록하는 방식. 키워드를 통해 문서를 검색
      - Distributed Sort : 데이터 정렬   
         - MAP 기능은 key를 추출. 각 Mapper에서 <key, value> 쌍을 출력. Reducer는 모든 쌍을 변경하지 않고 출력함. 
---

# **MapReduce Flow**
   <img src="https://user-images.githubusercontent.com/109563345/232403737-1a2b8ce4-02ac-43b0-bd1c-c1c4a0b8fcac.png" width="550">

   **1. split**   
      입력 데이터를 분할   
   **2. mapping**   
      입력 데이터를 받아 key-value를 생성하는 함수. 데이터의 필요한 부분만 선별하기 위한 데이터 필터링 과정   
   **3. shuffling**   
      Mapping 출력 데이터(중간 결과)를 Reducer로 이동. 이 때 중간 결과의 분배(partition) 및 정렬(sort) 작업   
      - partition : 맵의 결과 키를 리듀서로 분배하는 기준을 만드는 것. 기본 파티션으로 HashPartitioner   
      - sort : 리듀서로 전달된 데이터를 key 값 기준으로 정렬 (default 오름차순). 맵리듀스 프레임워크는 mapper의 output을 자동으로 정렬해서 reducer로 전달   
   **4. Reducer**   
      Map 함수의 출력 데이터를 받아 더 작은 값 집합으로 줄임. 그룹화 및 집계 등의 필요한 연산을 수행하여 최종 결과를 생성하는 함수   
   
   ***HashPartitioner**   
   기본 파티션을 나누는 방법으로 key값을 Hash코드로 바꾼 뒤, Reducer의 갯수로 그 Hash코드를 나눠서 나온 나머지로 Reducer를 정함   
   예시) Reducer 개수 2개   
   bad -> 30 / 2 = 15... 나머지 0 -> Reducer 0번   
   class -> 59 / 2 = 29... 나머지 1 -> Reducer 1번   
   
   <Br>
      
   ---
   
# **Total Order Sorting**
   각 Reducer는 Partitioner에 의해 할당된 중간 결과(key, value) 쌍을 받음. Reducer가 중간 결과를 수신하면 key별로 정렬되므로 일반적으로 Reducer의 출력도 key별로 정렬됨 → Reducer별로 부분 정렬은 가능함   
   그러나 서로 다른 Reducer의 출력은 서로 순서가 맞지 않아 전체 정렬은 불가능함

   ### **Total Order Sorting 방법**
   1. 사용자 지정 partitioning
   2. TotalOrderPartitioner를 사용해 partition 자동 생성

<Br>
   
   ## **1. 사용자 지정 partitioning**
   기본 partitioner가 아닌 partition에서 getPartition()메서드를 구현해서 각 reducer에 key를 할당하는 방법.   
   
   예시) key값이 알파벳으로 되어 있는 경우   
   Reducer 0 : A ~ I로 시작하는 key   
   Reducer 1 : J ~ Q로 시작하는 key   
   Reducer 2 : R ~ Z로 시작하는 key   
   
   문제점 : Reducer 간의 하중 분포가 같지 않음. key가 고르게 분포되어 있지 않을 확률이 높음
   

<Br>
   
   ## **2. TotalOrderPartitioner를 사용해 partition 자동 생성**
   1번 방법과 동일한 작업을 수행하지만 Reducer 간의 load balancing을 위해 각 파티션에 고르게 데이터 배분하는 방법을 Hadoop Mapreduce에서 제공   
   
   ### **Total sort 방법**      
   입력데이터를 샘플링하여 데이터 분포도 조사 후 partition을 나눔   
   1. 입력데이터를 샘플링해서 데이터의 분포도를 조사
   2. 데이터의 분포도에 맞게 파티션 정보를 미리 생성
   3. 미리 생성한 파티션 정보에 맞게 출력 데이터를 생성
   4. 각 출력 데이터를 병합
   
   ### **전체 정렬에 활용되는 TotalOrderPartitioner** 
   - InputSampler : 입력데이터에서 특정 개수의 데이터를 추출해 키와 데이터 건수를 샘플링 = 데이터 분포도 작성.
      - IntervalSampler(Input split에서 일정한 간격으로 키를 수집), RandomSampler(Input split에서 일정 확률로 키를 수집)   
   - TotalOrderPartitioner : 파티션개수와 파티션에 저장할 데이터범위를 설정   
   ![image](https://user-images.githubusercontent.com/109563345/232383664-49fff920-b3e0-4d16-855f-4aa1e1044baa.png)

   
---
   
출처   
[Total Order Sorting]<http://blog.ditullio.fr/2016/01/04/hadoop-basics-total-order-sorting-mapreduce/>   
[맵리듀스 전체 정렬]<https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=imf4&logNo=220734850771>, <https://lsh110600.github.io/de/2021/01/28/de-hadoop-ch6-part2/>
