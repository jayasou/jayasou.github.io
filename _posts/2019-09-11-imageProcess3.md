---
layout: post
title: 2-1. 구조체 메모리
comments: true
categories: [ComputerVision]
---

### 구조체 메모리 누수
~~~c++
struct BITMAPFILEHEADER {       // 14byte
    WORD bfType;                // BM
    DWORD bfSize;               // 파일 크기
    WORD bfReserved1;           // 0
    WORD bfReserved2;           // 0
    DWORD bfOffBits;            // HEADER 크기 ( BITMAPFILEH.. + BITMAPINFOH ..)
};
~~~
<br/><br/>

우리들은 위와 같이 헤더 정보를 구조체로 저장되도록 만들어 놓았다. <br/>
조금 더 간단하게 만들어보자. <br/><br/>

***TEST 구조체***
~~~c++
struct TEST {     
    char students[10];
    long period;
    short course;
};
~~~
<br/><br/>
***메모리 출력***
~~~c++
int main() {
    cout << "char[10] : " << sizeof(char)*10 << endl;
    cout << "short : " <<  sizeof(short) << endl;
    cout << "long : " <<  sizeof(long) << endl;
    cout << "TEST : " <<  sizeof(TEST) << endl;
    return 0;
}
~~~
<br/><br/>
***출력 결과***
~~~c++
    char[10] : 10
    int : 4
    long : 8
    TEST : 24
~~~
<br/>

> TEST의 메모리는 10 + 4 + 8 의 결과값 22가 되어야 한다. 

<br/><br/>

***왜 결과값이 다를까?***
<br/>
![memory](/images/memory.png)
<br/>

자료를 저장하는 방식과 읽어들이는 방식이 CPU마다 다르게 적용된다. <br/>
해당 컴퓨터는 4바이트씩 자료를 읽어들인다. <br/>
혹은 변수의 자료형을 변경했을 때 8바이트씩 읽어들인다. <br/><br/>


### 해결법 - 구조체 정렬하기

***pragma pack(push, n)***

~~~c++
#pragma pack(push, 2)
struct BITMAPFILEHEADER {       // 14byte나와야된다는디.
    WORD bfType;                // BM
    DWORD bfSize;               // 파일 크기
    WORD bfReserved1;           // 0
    WORD bfReserved2;           // 0
    DWORD bfOffBits;            // HEADER 크기 ( BITMAPFILEH.. + BITMAPINFOH ..)
};
#pragma pack(pop)
~~~
> pragma 이용해서 2바이트씩 push와 pop을 진행한다. 1바이트씩 push와 pop을 할 수도 있다.
1, 2, 4, 8, 16을 입력할 수 있다.
