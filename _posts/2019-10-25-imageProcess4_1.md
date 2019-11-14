---
layout: post
title: 3-1. 히스토그램분석
comments: true
categories: [ComputerVision]
---
### 히스토그램 분석

> 영상의 그레이스케일 값에 해당하는 픽셀의 개수를 표현

$$
h(g) = n_g
$$

$$
p(g) = \frac{n_g}{N}
$$

**g** : 그레이스케일 값 <br/> 
**n_g** : 그레이스케일 값이 g인 픽셀의 개수 <br/>
**p(g)** : 정규화과정을 통해 함수가 가질 수 있는 값을 고정한다. n_g에 영상 전체 픽셀의 개수로 나눈다.<br/>
**정규화하는 이유** : 
h(g) 함수가 가지는 값은 영상의 크기에 따라 달라질 수 있다. 이 때문에 정규화 과정을 통해 그레이스케일 값이 나타내는 확률의 개념으로 접근한다.
<br/>


~~~c++
Histogram() {
    size <- 영상 사이즈
    p[] <- 영상 픽셀

    cnt[256] <- 0으로 초기화
    // 필셀 값의 위치에 카운팅
    for(i from 0 to size) {
        cnt[p[i]]++;
    }
    // 히스토그램 정규화
    for(i from 0 to 256) {
        cnt[i] = cnt[i]/size;
    }
}
~~~

<hr>

#### 히스토그램 스트레칭

$$
\frac{f(x,y)-G_{min}}{G_{max}-G_{min}} * 255
$$

히스토그램이 그레이스케일 전 구간에서 골고루 나타나도록 하는 선형 변환 기법이다. 이를 통한 영상은 명암비가 높아져서 선명한 영상이 된다.

위의 식은 다음과 같이 변경할 수 있다.<br/>


$$
y = \frac{255}{max-min} * { x - min }
$$


<br/>
**(max - min)**은 영상의 **히스토그램 전체 범위**를 나타낸다. 8비트로 표현가능한 밝기 최대범위 255를 영상의 최대범위로 나눈 것이다. 이는 영상의 범위와 255와의 비를 나타낸다. **(x-low)는 영상의 픽셀 값에서 최저 밝기 값을 뺀 값이며, 이는, 최저 밝기와 얼마나 떨어져있는지를 의미**한다. <br/><br/>
**그렇다면 왜 이렇게 계산하는가** <br/>
min값이 0으로 재배치되며 영상에서 x와의 거리차 (x-min)에 스트레칭될 범위의 비 (255/(max-low))를 곱해주면 비율상 x가 어느 위치로 재배치되어야 하는지 알 수 있다.
<br/>

~~~c++
HistogramStretching() {
    size <- 영상 사이즈
    p[] <- 영상 픽셀

    gray_max <- p[0], gray_min <- p[0]
    for(i from 1 to size) {
        if(gray_max < p[i]) gray_max = p[i]
        if(gray_min > p[i]) gray_min = p[i]
    }

    for(i from 0 to size) {
        // p[i]를 이용한 스트레칭 연산
    }
}
~~~
<br/><br/>

#### 히스토그램 균등화 - 다시 공부하기

히스토그램의 누적 분포 특성에 근거하여 분포를 변경시키는 방법이다. <br/>
**PDF** : 확률 밀도 함수  <br/>
**CDF** : 누적분포  <br/>


~~~c++
HistogramEqualization() {
    size <- 영상 크기
    p[] <- 영상 픽셀

    histo[] = Histogram()

    cdf[256] = {0.0, }
    cdf[0] = histo[0]
    for(i from 1 to 256)
        cdf[i] = cdf[i-1] + histo[i]
    
    for(i from 0 to size)
        p[i] = cdf[p[i]] * 255
}
~~~

<hr>

[참고 사이트](https://blog.naver.com/PostView.nhn?blogId=xneokr&logNo=60123822863&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F)