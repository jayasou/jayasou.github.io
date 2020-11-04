---
layout: post
title: 윈도우즈 슬라이딩 알고리즘
comments: true
categories: [Algorithm]
---

### set
Tree , 중복된 원소들이 없다.
만약 중복된 원소들을 허락하고 싶다면 멀티셋(multiSet) 사용
redblack tree

### map
T ree, 중복된 원소들을 허락하지 않는다. 이미 같은 키가 원소로 들어있다면, 나중에 오는 insert는 무시된다.
중복된 원소들을 허락하고 싶다면 멀티맵(multiMap) 사용
중복된 원소들을 포함할 수 있기 때문에, 멀티맵에서는 [] 연산자를 제공하지 않음.
redblack tree


### unordered_set, unordered_map
set이나 map의 경우, 정렬된 상태로 내부에 저장되지만, 해당의 경우에는 정렬되지 않는다. hash

### 문제풀기
https://www.acmicpc.net/problem/2003
https://www.acmicpc.net/problem/2075
https://www.acmicpc.net/problem/2096
https://programmers.co.kr/learn/courses/30/lessons/67258

### 참고
https://blog.naver.com/kks227/220795165570
https://jujubebat.github.io/ps/pro67258/