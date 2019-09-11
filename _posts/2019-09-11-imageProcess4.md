---
layout: post
title: 3. 화질 향상 기법(그레이스케일)
comments: true
categories: [ComputerVision]
---

### 영상 반전

$$
g(x,y) = 255-f(x,y)
$$

> 그레이스케일 영상은 0~255 사이의 값을 가진다. 픽셀 값을 반전한다는 것은 0은 255로, 255는 0으로 바꾸는 것이다. 즉, 밝은 값은 어둡게, 어두운 값은 밝은 값으로 변경한다. 

<br/><br/>

### 밝기 조절

$$
g(x,y) = f(x,y) + n
$$

> 그레이스케일 값이 커질 수록 밝기가 밝아진다. 이때 값이 0보다 작거나 255보다 커지는 현상을 주의해야 한다. <br/> 픽셀 값은 unsigned char 형을 사용하는데 이 값이 정수 0보다 작아지면 자동으로 255인 값으로 값이 저장된다. 255보다 커질 경우 자동으로 0인 값이 저장된다. 그렇기 때문에 계산 후 값이 0보다 작을 경우엔 0으로, 255보다 클 경우 255로 저장되도록 검사해야 한다. 

<br/><br/>

### 명암비 조절

$$
g(x,y) = a * f(x,y)
$$

> 밝은 값은 더 밝게, 어두운 값은 더욱 어둡게 만들어 준다. 일반적으로 명암비가 높은 영상은 더 선명하다고 느낀다. <br/> a 값을 어떻게 설정하는지에 따라 영상이 달라진다. 원본 값에 일정한 수를 곱하면 전체 픽셀 값이 255로 바뀌는 현상이 나타날 수도 있다. <br/> 이를 방지하기 위에 그레이스케일의 중간값을 설정하여 다음과 같은 수식을 이용한다. (중간 값은 입력 영상의 전체 값의 평균으로 사용가능하다.)<br/>

$$
g(x,y) = f(x,y) + (f(x,y)-128) * a
$$

<br/><br/>

### 감마 보정

> 영상 센서, 모니터, 프린터 등에서 비선형적인 반응들을 보정하는 영상 처리 알고리즘

$$
s = T(r) =({\frac{r}{255}})^{\frac{1}{\gamma}} * 255
$$

<br/>

~~~c++
void IppGammaCorrection(IppByteImage& img, float gamma) {
    float inv_gamma = 1.f / gamma;

    int size = img.GetSize();
    BYTE* pixels = img.GetPixels();

    // 0.5f를 더하는 이유는 BYTE타입으로 형변환을 할 때
    // 반올림 효과를 주기 위해
    for(int i=0; i<size; i++)
        pixels[i] = static_cast<BYTE>(
            img.limit(pow((pixels[i]/255.f), inv_gamma) * 255 + 0.5f));
}
~~~

> 지수 연산을 위해 pow함수를 사용하였다. 위의 코드는 각 픽셀마다 한번씩 지수연산 함수를 지나게 된다. 이 방법은 픽셀의 수가 증가할수록 많은 비용을 요구한다. <br/> 코드를 최적화하기 위해 **pow함수에 대한 결과값을 배열에 미리 담아두어 재사용하는 룩업 테이블 기법을 사용한다.**

<br/><br/>

~~~c++
void IppGammaCorrectionOptimal(IppByteImage& img, float gamma) {
    float inv_gamma = 1.f / gamma;

    int size = img.GetSize();
    BYTE* pixels = img.GetPixels();

   float gamma_table[256];
   for(int i=0; i<256; i++)
       gamma_table[i] = pow( (i/255.f), inv_gamma);
   
   for(int i=0; i<size; i++)
       pixels[i] = static_cast<BYTE>( img.limit(gamma_table[pixels[i]] * 255 + 0.5f));
}
~~~~

> pow( (i/255.f), inv_gamma) 에 대한 값을 미리 계산해두면 픽셀 값마다 함수를 거치지 않고 그레이스케일 범위만큼 pow함수를 거치게 된다. 

<br/><br/>

### 히스토그램 분석

> 영상의 그레이스케일 값에 해당하는 픽셀의 개수를 표현

$$
h(g) = n_g
$$

$$
p(g) = \frac{n_g}{N}
$$

> g : 그레이스케일 값 <br/> 
n_g : 그레이스케일 값이 g인 픽셀의 개수 <br/>
정규화과정을 통해 함수가 가질 수 있는 값을 고정한다.

<br/><br/>

### 히스토그램 스트레칭

$$
\frac{f(x,y)-G_{min}}{G_{max}-G_{min}} * 255
$$

> 히스토그램이 그레이스케일 전 구간에서 골고루 나타나도록 하는 선형 변환 기법이다. 이를 통한 영상은 명암비가 높아져서 선명한 영상이 된다. 

<br/><br/>

### 히스통그램 균등화

> 히스토그램의 누적 분포 특성에 근거하여 분포를 변경시키는 방법이다. 