---
layout: post
title: 2. BMP파일 구조
comments: true
categories: [ComputerVision]
---

#### BMP파일 구조
<br/>

![BitMapFile](/images/BitMapFile.png){: width="80%" height="auto"}

<hr>

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
<hr>

#### BMP 파일 헤더
~~~c++
struct BITMAPFILEHEADER {       // 14byte
    WORD bfType;                // BM
    DWORD bfSize;               // 파일 크기
    WORD bfReserved1;           // 0
    WORD bfReserved2;           // 0
    DWORD bfOffBits;            // HEADER 크기 ( BITMAPFILEH.. + BITMAPINFOH ..)
};
~~~
<hr>

#### BMP 정보 헤더
~~~c++
typedef struct BITMAPINFOHEADER {       // 40byte
    DWORD biSize;               // BITMAPINFOHEADER 크기
    LONG biWidth;               // 영상의 가로
    LONG biHeight;              // 영상의 세로
    WORD biPlanes;              // 항상 1
    WORD biBitCount;            // 한 픽셀당 비트수 ( 8일 경우 2^8 으로 256 색상 사용한다는 의미)
                                // 24일 경우 트루컬러 비트맵이기 때문에 색상테이블이 존재하지 않음.
    DWORD biCompression;        // 항상 0
    DWORD biSizeImage;          // biWidth*biHeight*(biBitCount/8)
                                // 만약 가로크기가 4의 배수가 아닐 때 4의 배수로 맞추어서 영상의 크기 계산됨
    // 이하 생략
    LONG biXpelsPerMeter;
    LONG biYPelsPerMeter;
    DWORD biClrUsed;
    DWORD biClrImportant;
}BITMAPINFOHEADER, *LPBITMAPINFOHEADER;
~~~
<hr>

#### 색상테이블 (팔레트)
~~~c++
struct RGBQUAD {
    BYTE rgbBlue;
    BYTE rgbGreen;
    BYTE rgbResd;
    BYTE rgbReserved;           // 항상 0
};
~~~

<hr>

#### 자료형 알고가기
필자는 현재 MacOS를 사용중이다. 윈도우를 사용한다면 MFC에서 제공하는 헤더를 이용할 수 있다. <br/>
하지만 해당 헤더를 사용할 수 없다면 이 부분은 꼭 필요하다. <br/>

1. ***Window*** <br>
윈도우OS를 사용한다면 해당 타입의 정의는 다음과 같다.
~~~c++
typdef unsigned char BYTE;      // 1Byte
typedef unsigned short WORD;    // 2Byte
typedef unsigned long DWORD;    // 4Byte
typedef long LONG;              // 4Byte
~~~

2. ***Mac*** <br>
Mac사용자인 경우엔 다음과 같이 정의하자.
~~~c++
#define BYTE unsigned char      // 1byte
#define WORD unsigned short     // 2byte
#define DWORD unsigned int      // 4byte
#define LONG int                // 4byte
~~~

윈도우에서는 굳이 정의할 필요가 없다. <br/>
하지만 mac에서는 제공하는 헤더를 사용할 수 없으니 위와 같은 방법으로 해당 자료형의 표현을 정의해두면 된다.

<hr>

#### BMP파일 자세히 보기

![bitmapInfo](/images/bitmapInfo.png){: width="80%" height="auto"}
<br/><br/>

* bfType : 비트맵 파일임을 명시한다. 'BM'이라는 값 저장
* bfSize : BMP파일의 크기
* bfReserved1~2: 예약된 값. 항상 0
* bfOffBits : BITMAPFILEHEADER 크기 + BITMAPINFOHEADER 크기 + 색상테이블 크기
<br/>

* biSize : BITMAPINFOHEADER 구조체의 크기 (일반적으로 40Byte이지만 아닌 경우엔 DIB이다.)
* biWidth : 비트맵 가로 크기인 픽셀 단위
* biHeight : 비트맵의 세로크기인 픽셀 단위
    * 양수인 경우 픽셀 데이터는 상하가 뒤집힌 상태
    * 음수일 경우 정상적인 상태
* biPlane : 비트맵을 화면에 보여줄 때 필요한 플레인 수. 일반적으로 1이다.
* biBitCount : 픽셀 하나를 표현하기 위해 필요한 비트 수
    * 1, 4, 8, 16, 24, 32의 값을 가질 수 있다.
* biCompression : 입축 유형
* biSizeImage : DIB구조에서 픽셀 데이터를 저장하는데 필요한 메모리 공간의 크기
<br/><br/>
... 나머지 생략 ...
<hr>

#### 픽셀 데이터
일반적으로 비트맵은 상하가 뒤집힌 상태로 저장이 된다. 이때 180도 회전한 상태가 아니라는 점을 주의! <br/>
비트맵 형태로 저장될 때 영상의 가로 크기를 4의 배수 형태로 저장한다. <br/>
3x3인 원본영상이라도 4x3의 크기로 저장된다. 즉, 한 행마다 1바이트가 더 추가된다는 뜻이다.<br/>
트루컬러 영상일때는 어떨까<br/>
트루컬러는 한 픽셀당 RGB 3Byte가 저장된다. 여분의 픽셀 값도 3Byte가 저장이 되어야 한다.<br/>
3x3 인 영상이고 트루컬러일 때 총 12Byte가 저장된다.<br/>
<hr>

* 원본 영상 ( 한칸당 1Byte)

|   |   |   |
|---|---|---|
|idx|idx|idx|
|idx|idx|idx|
|idx|idx|idx|

* 그레이스케일 비트맵 ( 한칸당 1Byte)

|   |   |   |   |
|---|---|---|---|
|idx|idx|idx|0|
|idx|idx|idx|0|
|idx|idx|idx|0|

* 트루컬러 비트맵 ( 한 칸당 3Byte )

|   |   |   |   |
|---|---|---|---|
|RGB|RGB|RGB|000|
|RGB|RGB|RGB|000|
|RGB|RGB|RGB|000|
