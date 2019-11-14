---
layout: post
title: 6. 영상의 기하학적인 변환
comments: true
categories: [ComputerVision]
---

### 이동변환

영상을 가로 또는 세로 방향으로 특정 크기만큼 이동시키는 변환이다. <br>
(x,y)를 가로로 a만큼, 세로로 b만큼 이동시키는 이동 변환 수식을 표현하면 다음과 같다.

$$
	\acute{x} = x + a\\
	\acute{y} = y + b
$$

![translate](/images/translate.png){: width="60%" height="auto"}

~~~c++
// sx , sy 는 이동할 크기
Translate(imgSrc, imgDst, int sx, int sy) {
	// imgDst 초기화

	pSrc[][] = imgSrc.pixels
	pDst[][] = imgDst.pixels

	for(j from 0 to h-1)
	for(i from 0 to w-1) {
		x = i - sx;
		y = j - sy;

		if(x >= 0 AND x < w AND y >= 0 AND y < h)
			pDst[j][i] = pSrc[y][x]
	}
}
~~~

<hr>

### 크기변환
$$
\acute{x} = s_x*x\\
\acute{y} = s_y*y
$$

크기 변환은 입력 영상의 크기를 가로 방향으로 s_x, 세로 방향으로 s_y배로 변환한다. <br>
![resize](/images/resize.png){: width="60%" height="auto"}

<hr>

**순방향 매핑의 문제점** <br>

~~~c++
for(j from 0 to h-1)
for(i from 0 to w-1) 
	pDst[2*j][2*i] = pSrc[j][i]
~~~

위와 같이 동작을 할 경우 **빈 홀**이 생기게 된다. 

$$
\begin{bmatrix}
2&4\\
8&12
\end{bmatrix} \longrightarrow
\begin{bmatrix}
2&?&4&?\\
?&?&?&?\\
8&?&12&?\\
?&?&?&?
\end{bmatrix}
$$

이런 문제점은 **역방향 매핑**으로 해결한다. <br>
역방향 매핑에서는 결과 영상의 각각의 픽셀에 대하여 대응되는 입력 영상의 픽셀 위치를 찾아 그 위치에서의 픽셀 값을 참조한다. 

$$
x = \acute{x}/s_x \\
y = \acute{y}/s_y
$$

픽셀의 좌표는 모두 정수이어야 한다. <br>
위의 식으로 계산했을 때, x,y 값이 실수형으로 계산되는데 이를 결정하기위해 **보간법**을 이용한다. <br>
보간법은 주변 픽셀 값들을 이용하여 원하는 위치의 값을 추정하는 방법이다. <br>

<hr>

#### 1. 최근방 이웃 보간법

최근방 이웃 보간법은 빠르고 구현하기 쉽다. <br>
하지만 픽셀 값이 급격하게 변화하는 **계단현상**이 나타날 수 있다. <br>

~~~c++
// nw, nh 는 결과 영상의 가로와 세로 크기
ResizeNearest(imgSrc, imgDst, nw, nh) {
	// imgDst 초기화..
	pSrc[][] = imgSrc.pixels
	pDst[][] = imgDst.pixels

	for(j from 0 to nh-1)
	for(i from 0 to nw-1) {
		rx = (w-1) * i / (nw - 1)
		ry = (h-1) * j / (nh - 1)

		x = rx의 정수형
		y = ry의 정수형

		pDst[j][i] = pSrc[x][y]
	}
}
~~~

결과 영상의 좌푯값을 가로, 세로 확대 비율로 나누어주고, <br>
그 값을 정수형으로 변환하여 참조할 원본 영상의 좌표를 사용하는 방식이다. 

<hr>

#### 2. 양선형 보간법

실수 좌표를 둘러싸고 있는 네 개의 픽셀 값에 가중치를 곱한 값들의 선형 합으로 결과 영상의 픽셀 값을 구하는 방법이다. <br> 
다음의 그림과 같이 최종 픽셀 값 z를 구할 수 있다. 
좌표에서 픽셀 값은 a,b,c,d이다.

![resizeBilinear](/images/resizeBilinear.png){: width="60%" height="auto"}

z를 구하는 수식은 다음과 같다.

$$
	z = (1-p)(1-q)a + p(1-q)b + (1-p)qc + pqd
$$

~~~c++
ResizeBilinear(imgSrc, imgDst, nw, nh) {
	// imgDst 초기화..
	pSrc[][] = imgSrc.pixels
	pDst[][] = imgDst.pixels

	for(j from 0 to nh-1)
	for(i from 0 to nw-1) {
		rx = (w-1) * i / (nw - 1)
		ry = (h-1) * j / (nh - 1)

		x1 = rx의 정수형
		y1 = ry의 정수형

		x2 = x1+1
		y2 = y1+1

		p = rx - x1
		q = ry - y1

		value = (1-p) * (1-q) * pSrc[y1][x1]
				+ p * (1-q) * pSrc[y1][x2]
				+ (1-p) * q * pSrc[y2][x1]
				+ p * q * pSrc[y2][x2]

		pDst[j][i] = value의 정수형
	}
}
~~~

![resizeBilinear2](/images/resizeBilinear2.png){: width="70%" height="auto"}

x1, x2, y1, y2는 (rx, ry) 좌표를 둘러싼 4개의 픽셀 좌표이다. <br>
p와 q는 0부터 1 사이의 값을 갖는 실수이다. <br>

<hr>

#### 3. 3차 회선 보간법 (...?)

<hr>

### 회전변환

$$
\acute{x} = cos{\theta}*x - sin{\theta}*y\\
\acute{y} = sin{\theta}*x + cos{\theta}*y
$$

회전변환을 구현할 때에도 역방향 매핑을 사용해야 비어 있는 픽셀이 생기지 않는다.

$$
x=cos{\theta}*\acute{x} + sin{\theta}*\acute{y}\\
y=-sin{\theta}*\acute{x} + cos{\theta}*\acute{y}
$$

#### 특수각도 회전

**90도 회전** <br>
$$
\acute{x} = h -1 -y\\
\acute{y} = x
$$
<br>

**180도 회전** <br>
$$
\acute{x} = w -1 -x\\
\acute{y} = h -1 -y
$$
<br>

**270도 회전** <br>
$$
\acute{x} = y\\
\acute{y} = w -1 -x
$$

<hr>

### 대칭변환 
<br>

#### 1. 좌우대칭
$$
\acute{x} = w -1 -x\\
\acute{y} = h -1 -y
$$

<hr>

#### 2. 상하대칭
$$
\acute{x} = x\\
\acute{y} = h -1 -y
$$
<br>

[참조](http://blog.daum.net/shksjy/220)