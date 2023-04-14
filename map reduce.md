---
layout: post
title: "map reduce"
등록일: 2023-04-13
수정일: 2023-04-13
author: 이한솔
---

## **목차**
1. distributed sort

<Br>
   
---

# **MapReduce Flow**
   <img src="https://user-images.githubusercontent.com/109563345/231960330-7183dce1-2edc-41d6-a450-3efab02edd0a.png" width="550">
   
1. split   
   입력 데이터를 분할
2. mapping   
   입력 데이터를 받아 key-value를 생성하는 함수. 데이터의 필요한 부분만 선별하기 위한 데이터 필터링 과정
3. shuffing   
   Mapping 출력 데이터를 Reducer로 이동. 이 때 중간 데이터의 재분배(partition) 및 정렬(sort) 작업
   - partition : 맵의 결과 키를 리듀서로 분배하는 기준을 만드는 것. 기본 파티션으로 HashPartitioner
   - sort : 리듀서로 전달된 데이터를 key 값 기준으로 정렬 (default 오름차순). 맵리듀스 프레임워크는 mapper의 output을 자동으로 정렬해서 reducer로 전달
4. Reducer   
   Map 함수의 출력 데이터를 받아 더 작은 값 집합으로 줄임. 그룹화 및 집계 등의 필요한 연산을 수행하여 최종 결과를 생성하는 함수

# **Total Order Sorting**
각 Reducer는 Partitioner에 의해 할당된 (key, value) 쌍을 받는다. Reducer가 중간 데이터를 수신하면 key별로 정렬되므로 일반적으로 Reducer의 출력도 key별로 정렬됨.   
그러나 서로 다른 Reducer의 출력은 서로 순서가 맞지 않음.
   

---
   
출처   
[Total Order Sorting]<http://blog.ditullio.fr/2016/01/04/hadoop-basics-total-order-sorting-mapreduce/>
