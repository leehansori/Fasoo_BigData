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

# **numpy vs pandas 성능 비교 건**
   <img src="https://user-images.githubusercontent.com/109563345/231960330-7183dce1-2edc-41d6-a450-3efab02edd0a.png" width="500">   
1. split   
   입력 데이터를 분할
2. mapping   
   입력 데이터를 받아 key-value를 생성하는 함수. 데이터의 필요한 부분만 선별하기 위한 데이터 필터링 과정
3. shuffing   
   - shuffle : 리듀서로 데이터 이동
   - sort : 리듀서로 전달된 데이터를 key 값 기준으로 정렬 (default 오름차순)
4. Reducing   
   Map 함수의 출력 데이터를 받아 그룹화 및 집계 등의 필요한 연산을 수행하여 최종 결과를 생성하는 함수
