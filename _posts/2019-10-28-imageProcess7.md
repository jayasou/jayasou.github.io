---
layout: post
title: 5-1. 영상 스무딩
comments: true
categories: [ComputerVision]
---

### 공간적 필터링

![filter](/images/filter.png){: width="80%" height="auto"}
<br>
영상을 부드럽게 만들거나, 엣지를 강조하여 날카롭게 만드는 **마스크 연산**은 입력을 어떤 시스템에 통과시켰을 때 전혀 다른 함수의 출력을 나타낸다. <br>

$$
	g(x,y) = \sum_{j=-1}^{1}\sum_{i=-1}^{1}m(i,j)f(x+i, y+j)	
$$

마스크의 크기는 3x3, 5x5, 7x7등 다양한 크기를 사용할 수 있다.<br>
마스크의 전체 합은 1이 되어야 한다. <br>
1보다 커질 경우, 결과 영상은 전체적인 밝기 값이 증가한다. 1보다 작을 경우, 밝기 값이 감소하게 된다. <br>
마스크 연산을 이용할 때, 영상의 최외곽 픽셀에 대해서 따로 처리를 해주어야 한다. <br>

* 중첩 부분만 처리
	* 최외곽 픽셀은 마스크 연산에서 제외한다.
* 가상의 픽셀
	* 최외곽 바깥에 0 또는 주변의 픽셀 값을 복사하여 사용한다.
 
<hr>

#### 평균값 필터 

$$
 \frac{1}{9}*\begin{bmatrix}
1&1&1\\
1&1&1\\
1&1&1
\end{bmatrix}
$$

필터 마스크 내에 숫자들의 합으로 전체를 나눠준 것과 같다. <br>
평균 값 필터 마스크의 크기가 커질수록 더욱 부드러운 결과영상이 생성된다. <br>
전체 합이 1이되어야 하기 때문에 3x3 필터는 전체 1행렬에 9를 나누어주게 된다. <br>

~~~c++
FilterMean(imgSrc, imgDst) {
	// 입력 영상 img와 동일한 복사본 영상
	// 픽셀 값 참조해야 중복 연산이 발생 방지
	imgDst = imgSrc

	pSrc[][] = imgSrc.pixels
	pDst[][] = imgDst.pixels

	mask[3][3] = {
		{1,1,1},
		{1,1,1},
		{1,1,1}
	}

	for(j from 1 to height-1)
	for(i from 1 to width-1) {
		sum = 0
		for(m from 0 to 2)
		for(n from 0 to 2) {
			sum += pSrc[j-1+m][i-1+n] * mask[m][n]
		}

		pDst[j][i] = sum
	}
}
~~~
<hr>

#### 가중 평균 값 필터

평균 값 필터의 경우 마스크의 크기가 커질수록 원래 픽셀의 정보가 감소된다. <br>
이를 보안하기 위해, 가운데 위치한 픽셀에 가중치를 주는 방법이 있다. <br>

$$
 \frac{1}{16}*\begin{bmatrix}
1&2&1\\
2&4&2\\
1&2&1 
\end{bmatrix}
$$

일반 평균 값 필터결과보다 원복 픽셀의 정보를 많이 간직하고 있기 때문에, 엣지 부분이 더 선명한 결과를 보여준다. 

<hr>

#### 가우시안 필터

$$
	G_\sigma(x) = \frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{x^2}{2\sigma^2}}
$$

![Gaussian1](/images/Gaussian1.png){: width="80%" height="auto"}

Sigma 값이 클수록 높이는 낮지만 폭은 넓어지므로, 많은 저주파 성분을 통과시킨다. <br>
반면에, Sigma값이 작을 수록 적은 저주파 성분만 통과시킨다. <br><br>
영상과 같은 2차원 공간에서의 가우시안 함수는 다음과 같다.

$$
	G_\sigma(x, y) = \frac{1}{\sqrt{2\pi}\sigma^2}e^{-\frac{x^2+y^2}{2\sigma^2}}
$$

![Gaussian2](/images/Gaussian2.png){: width="80%" height="auto"} <br>

![Gaussian2](/images/Gaussian3.png){: width="80%" height="auto"} <br>

마스크의 크기가 커질수록 연산 속도가 급격히 증가한다. 때문에 가우스 필터를 수행해야 할 경우에는 2차원 함수를 1차원 함수로 변형한다. <br>

$$
	G_\sigma(x) = \frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{x^2}{2\sigma^2}} * \frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{y^2}{2\sigma^2}}
$$

x방향으로의 함수와 y방향으로의 함수로 각각 분리한다. <br>
입력 영상을 x방향과 y방향으로 1차원 마스크 연산을 수행한다.

<hr>

[Visual c++ 영상처리 프로그래밍](https://thebook.io/006796/ch08/02/03/)
