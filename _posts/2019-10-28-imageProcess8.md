---
layout: post
title: 5-2. 영상 샤프닝과 잡음생성
comments: true
categories: [ComputerVision]
---

### 샤프닝

영상의 윤곽선 부분을 강조하는 효과를 주는 필터이다. <br>
저주파 성분보다 고주파를 강조한다. <br>

<hr>

#### 언샤프 마스크 필터링

$$
	g(x,y) = f(x,y) - \bar{f}(x,y)
$$

g(x,y) 입력영상 f(x,y)와 입력영상을 스무딩한 결과영상의 합이다. <br>
g(x,y)는 엣지 부분에 해당하는 픽셀에서 큰 양수 또는 음수 값을 가지게 된다. 평탄한 영역은 0에 가까운 값을 갖는다. <br>
이를 통해, 원래 영상 f(x,y)에 g(x,y)를 더해주면 최종적으로 날카로운 영상을 만들 수 있다. <br>
최종 수식은 다음과 같다.

$$
	h(x,y) = f(x,y) + g(x,y)
$$

<hr>

#### 라플라시안 언샤프 마스크 필터링

실제 구현에서는 이차 미분을 이용한 마스크 연산을 많이 사용한다. <br>

![unsharp](/images/unsharp.png){: width="50%" height="auto"}

* 그림 (a) : 입력 영상에서 엣지 부분의 값의 변화를 1차원 함수로 표현
* 그림 (b) : 1차원 함수를 미분한 결과
* 그림 (c) : 2차원 함수를 미분한 결과
	* 2차 미분한 결과는 원본 영상에서 엣지 부분에서만 0이 아닌 값을 갖는 형태이다.
* 그림 (d) : 원본 함수에서 이차 미분 함수를 빼면, 엣지가 강조된 날카라운 영상을 만들 수 있다.

<br>
이차 미분을 나타내는 **라플라시안**은 마스크 형태로 나타낼 수 있다. <br>
이 결과는 앞의 그림 중 (c)와 일치하는 영상을 만들어 낸다.

**4방향**
$$
\begin{bmatrix}
0&1&0\\
1&-4&1\\
0&1&0
\end{bmatrix}
$$

**8방향**
$$
\begin{bmatrix}
1&1&1\\
1&-8&1\\
1&1&1
\end{bmatrix}
$$

여기까지는, 라플라시안, 즉 2차미분하는 방법에 대해서 알아보았다. <br>
이 과정 다음에 샤프닝을 하기 위해서는 언샤프 마스크 필터링과 같은 방법을 적용한다.

$$
	h(x,y) = f(x,y) - \bigtriangledown f(x,y)
$$

라플라시안을 이용한 언샤프 마스크는 다음과 같다. <br>
가우시안 필터와 유사하다. <br>

**4방향**
$$
\begin{bmatrix}
0&-1&0\\
-1&5&-1\\
0&-1&0\\
\end{bmatrix}
$$

**8방향**
$$
\begin{bmatrix}
-1&-1&-1\\
-1&9&-1\\
-1&-1&-1\\
\end{bmatrix}
$$

<hr>

#### 하이부스트 필터

하이부스트 필터는 영상의 명암비를 높이면서, 엣지를 강조한다. <br>
전체적으로 더 선명한 결과영상을 만들어 낼 수 있다. <br>

$$
h(x,y) = \alpha * f(x,y) + \bigtriangledown^2 f(x,y)
$$

~~~c++
sum = (5 + alpha)*pSrc[j][i] - pSrc[j-1][i]
		- pSrc[j][i-1] - pSrc[j+1][i] - pSrc[j][i+1]
~~~

<hr>

### 잡음생성
영상에서 잡음은 픽셀 값에 추가되는 원치 않는 형태의 신호이다. <br>

<hr>

#### 가우시안 잡음생성
보통 카메라 센서에서 추가되는 잡음은 가우시안 분포 함수의 형태를 따른다. <br>
평균이 0이고, 표준 편차가 1인 가우시안 분포를 정상 분포라고 한다. <br>
정상 분포 난수 발생기를 사용하여 랜덤적으로 가우시안 난수를 생성할 수 있다. <br>
생성한 난수는 가우시안 분포를 따른다. <br>

**가우시안 난수 생성**
~~~c++
unsigned int seed = static_cast<unsigned int>(time(NULL));
std::default_random_engine generator(seed);
std::normal_distribution<double> distribution(0.0, 1.0);
~~~

default_random_engine 객체 선언할 때, 임의의 숫자 값 seed 를 부여해야 한다. <br>
seed는 시스템 시간을 초단위로 가져와서 사용하였다. <br>
<hr>

**가우시안 잡음생성 Code**
~~~c++
// amount : 각 픽셀마다 추가되는 잡음의 크기
NoiseGaussian(imgSrc, imgDst, int amount) {
	size <- imgSize
	imgDst = imgSrc;

	// 난수 생성기
	distribution...generator...

	for(i from 0 to size) {
		rn = distribution(generator) * 255 * amount/100;
		pDst[i] = rn;
	}
}
~~~

<hr>

#### 소금&후추
소금과 후추처럼 흰색, 검정색으로 이루어진 잡음이다. <br>
앞서 말한, 가우시안 잡음은 영상 내의 모든 픽셀에 대해 잡음을 추가하는 형태로 구현하였다. <br>
소금후추잡음은 영상 내의 임의의 픽셀에 대해서만 그 값을 0 또는 255로 설정하면 된다. <br><br>

임의의 픽셀을 선택하기 위해 rand함수를 이용한다. <br>
예를 들어, 0부터 100사이의 난수 10개를 발생시키려면 다음과 같은 형태로 코드를 작성한다.

~~~c++
unsigned int seed = static_cast<unsigned int>(time(0));
std::default_random_engine generator(seed);
std::uniform_int_distribution<int> distribution(0, 100);

for(i from 0 to 9)
	std::cout << distribution(generator) << std::endl;
~~~
<hr>

**소금&후추 잡음생성 Code**
~~~c++
// amount : 각 픽셀마다 추가되는 잡음의 크기
NoiseSaltNPepper(imgSrc, imgDst, int amount) {
	size <- imgSize
	imgDst = imgSrc;

	// 난수 생성기
	distribution...generator...

	num = size * amount / 100;
	for(i from 0 to num)
			pDst[distribution(generator)] = (i & 0x01) * 255
}
~~~

amount 값이 20이면 잡음이 추가될 픽셀의 개수는 100x100x(20/100), 즉 2000개이다. <br>
잡음이 추가될 좌표는 distribution(generator) 코드를 사용하였다. <br>
또한, i가 짝수이면 0이고, 홀수이면 255가 대입되도록 하였다.
<hr>

### 잡음제거
<hr>

#### 미디언필터
미디언 필터는 입력 영상에서 주변 픽셀들의 값들을 오름 또는 내림차순으로 정렬하여 그 중앙에 있는 값으로 픽셀 값을 대체하는 방식이다. 마스크 값과 픽셀의 값을 곱하는 선형 계산법이 아닌 비선형 공간적 필터링 기법이다. 동작과정은 다음과 같다. <br>

$$
\begin{bmatrix}
73&142&156\\
52&82&120\\
64&102&85
\end{bmatrix} \longrightarrow
\begin{bmatrix}
73\\142\\156\\52\\82\\120\\64\\102\\85
\end{bmatrix} \longrightarrow
\begin{bmatrix}
52\\64\\73\\82\\\bold{85}\\102\\120\\142\\156
\end{bmatrix} \longrightarrow
\begin{bmatrix}
73&142&156\\
52&\bold{85}&120\\
64&102&85
\end{bmatrix}
$$

자기 자신과 주변 8개의 픽셀 값을 일렬로 늘여 세운 후, 이를 크기 순으로 정렬한다. 정렬된 데이터에서 중앙에 있는 픽셀 값 85가 대치될 값이다. 원래 픽셀 값 82를 85로 교체한다. <br>

~~~c++
FilterMedian(imgSrc, imgDst) {
	imgDst = imgSrc

	pSrc[][] = imgSrc.pixels
	pDst[][] = imgDst.pixels

	BYTE m[9]
	for(i from 1 to h-1)
	for(j from 1 to w-1) {
		// m 초기화 <- pSrc[j][i]의 주변값 채우기
		// 정렬
		std::sort(m, m+9)
		pDst[j][i] = m[4]
	}
}
~~~
<hr>

### 비등방성 확산 필터 (...?)
