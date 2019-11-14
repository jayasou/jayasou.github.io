---
layout: post
title: 4. 영상의 연산
comments: true
categories: [ComputerVision]
---

#### 덧셈 연산 (뺄셈 연산도 마찬가지)

$$ 
h(x,y) = f(x,y) + g(x,y)
$$

~~~c++
Add(img1, img2) {
    size <- imgSize

    p1[] <- img1.pixels
    p2[] <- img2.pixels
    new[] <- new Array[size]

    for(i from 0 to size)
        new[i] = p1[i] + p2[i]
}
~~~

<hr>

#### 평균 연산

$$ 
h(x,y) = \frac{f(x,y) + g(x,y)}{2}
$$

* 두 영상의 합성효과
* 잡음 제거 - 여러 사진을 찍은 후 평균 연산을 통해 새로운 영상을 만들어내면 잡음이 제거되는 효과

<hr>

#### 차이 연산
$$
h(x,y) = |f(x,y) - g(x,y)|
$$

차이연산은 입력영상의 순서와는 상관없이 두 영상의 차이점을 나타낼 수 있다. 앞서 말한 뺄셈 연산은 입력영상의 순서에 의해 결과 영상이 달라질 수 있다. 차이 연산은 자동화 시스템의 결함을 찾거나 침입자를 감지할 수 있다.

~~~c++
Diff(img1, img2) {
    size, p1, p2, new 초기화
    
    for(i from 0 to size) {
        diff = p1[i] - p2[i]
        new[i] = (diff>=0) ? diff : -diff
    }
        
}
~~~

<hr>

#### AND 연산

$$
    h(x,y) = f(x,y) & g(x,y)
$$

$$
    \frac{00001111}{11001000} = 00001000
$$

대입값이 모두 1일 때를 제외하고, 모두 0을 취하는 논리연산이다. <br>
대입 영상의 픽셀보다 작은 값은 모두 0으로 바뀐다.

<hr>

#### OR 연산

$$
    h(x,y) = f(x,y) | g(x,y)
$$

$$
    \frac{00001111}{11001000} = 11001111
$$

대입값이 모두 0일 때를 제외하고, 모두 1을 취하는 논리연산이다. <br>
대입 영상의 픽셀값 보다 큰 값은 255로 바뀐다.


