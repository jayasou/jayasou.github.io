---
layout: post
title: 1. 영상처리를 위한 기본적인 개념익히기
comments: true
categories: [ComputerVision]
---

### 영상표현

#### 그레이스케일

* 밝기 정보만으로 구성 ( 검, 흰, 회 )
* 하나의 픽셀은 0~255 사이의 정숫값을 가진다.
> 하나의 픽셀은 1바이트메모리 공간을 필요로한다. 1바이트는 256가지의 색상을 표현할 수 있다.

#### 트루컬러
* 색상 정보
> 트루컬러의 각 픽셀은 0~255 범위를 갖는 R, G, B 세 성분으로 구성

### 픽셀 
* 영상의 기본 단위 
* '화소'

<hr>

### c++ 2차원 배열 처리하기

1. Unsigned char 자료형은 1바이트 크기를 가지며 0~255까지의 정수를 저장할 수 있다.
    * unsigned가 없는 char 자료형은 -128 ~ 127 까지의 정수를 표현가능



2. 2차의 배열 크기 지정
    * x축 640, y축 480인 배열을 만들 때 unsigned char a[200][128] 과 같이 정의
    * 2차원 배열을 선언하면 실제 메모리 공간에는 배열 크기만큼 메모리 공간이 연속적으로 할당된다
        > 640 *480 = 307200 = > unsigned char a[307200]
    * 일반적으로 지역 변수로 사용할 수 있는 메모리 영역에는 한계가 있기 때문에 대략 1024*1024보다 큰 배열을 만들수 없다.
그렇기 때문에 실제 영상처리 프로그래밍에서는 정적 배열 대신 프로그램 동작 시 배열의 크기를 결정할 수 있는 동적배열을 주로 사용
    * 포인터형 변수는 32비트 운영체제에서는 4바이트 크기를 갖고, 64바이트 운영체제에서는 8바이트 크기를 갖는다.
실제 픽셀 값이 저장되는 데이터 공간은 unsinged char 타입이므로 각각 1바이트 크기를 갖는다.
    * 지역변수로 선언된 정적 배열은 블록 또는 함수가 종료되면 자동으로 메모리 공간이 사라지지만, 동적 배열은 사용자가 직접 메모리공간을 해제해주어야 한다. 
동적 할당된 2차원 배열의 메모리 해제 방법은 동적 배열을 할당하는 방법의 반대이다. 

<hr>

### 간단한 2차원배열 만들기1

<br/>

* 배열 생성 및 초기화
~~~c++
    int height = 3;
    int width = 4;
    // 1. 2차원 배열 생성
    unsigned char** arr;

    // 2. 배열 0으로 초기화
    arr = new unsigned char*[height];
    for(int i=0; i<height; i++) {
        arr[i] = new unsigned char[width];
        
        // void *memset(void *dest, int c, size_t count);
        // arr[i]가 가리키는 메모리 공간으로 부터 sizeof(unsigned char)*width에 해당하는 크기의 메모리 공간을
        // 모두 0으로 채우라는 의미
        
        memset(arr[i], 0, sizeof(unsigned char)* width);
    }
~~~
<br/>

* 메모리해제
~~~c++
    for(int i=0; i<height; i++) {
        delete[] arr[i];
    }
    delete[] arr;
~~~
<br/>

![arr1](/images/arr1.png)

<hr>

### 간단한 2차원배열 만들기2
<br/>

* 배열 생성 및 초기화
~~~c++
    int height = 3;
    int width = 4;
    // 1. 2차원 배열 생성
    unsigned char** arr;

    // 2. 배열 0으로 초기화
    arr = new unsigned char*[height];
    arr[0] = new unsigned char[width * height];
    for(int i=1; i<height; i++)
        arr[i] = arr[i-1] + width;
    memset(arr[0], 0, width * height);
~~~
<br/>

* 메모리해제
~~~c++
    delete[] arr[0];
    delete arr;
~~~
<br/>

![arr2](/images/arr2.png) {: width="60%" height="auto"}
