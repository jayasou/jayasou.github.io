---
layout: post
title: 2. BMP파일 구조
comments: true
categories: [ComputerVision]
---

### BMP파일 구조
<br/>

![BitMapFile](/images/BitMapFile.png)
<br/>

* 비트맵 파일 헤더
    * 파일 자체에 대한 정보 영역
* 비트맵 정보 헤더
    * 비트맵 영상 크기, 색상 수 등의 정보 영역
* 색상 테이블
    * 비트맵의 색상 정보
    * 색상이 256 이하이면, 색상 테이블이 존재한다. 픽셀 데이터는 색상테이블의 인덱스를 표현한다.
    * 트루컬러 영상에는 색상 테이블이 존재하지 않는다. 픽셀 데이터에 RGB색상 정보를 표현한다.
* 픽셀 데이터
    * 색상 정보 표현
        * 그레이스케일 : 각 픽셀당 1바이트를 사용하여 색상 테이블의 인덱스를 가리킨다.
        * 트루컬러 : 각 픽셀당 3바이트를 사용하여 파란색, 녹색, 빨간색 생상 정보 표현한다.