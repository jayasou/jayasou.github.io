---
layout: post
title: 7. 리스트
comments: true
categories: [ComputerVision]
---


### 푸리에변환
#### 1차원 데이터에 대한 이산 푸리에 변환
#### 2차원 영상의 푸리에 변환
#### 개선된 2차원 영상의 푸리에 변환
#### 고속 푸리에 변환
#### 2차원 영상의 고속 푸리에 변환

### 주파수 공간에서 필터링
#### 저역, 고역 통과 필터
#### 가우시안 저역, 고역 통과 필터

<hr>

### 영상의 특징값 추출 방법

<hr>

#### 엣지 검출
일반적으로 객체와 배경의 경계에서는 픽셀의 밝기 값이 크게 변한다. <br>
이때, 픽셀 밝기 값의 변화가 큰 부분을 엣지로 판별할 수 있다. <br>

영상에서 엣지를 검출하기 위해서는 영상을 미분한 후, 미분 값이 특정 임계 값보다 큰 부분을 찾는다.

![edge1](/images/edge1.png){: width="50%" height="auto"}

영상은 2차원 공간에서 정의된 함수이기 때문에, 영상에서는 가로 방향과 세로 방향의 미분 값을 함께 사용해야 한다. 2차원 함수의 그래디언트는 벡터이기 때문에 크기와 방향으로 나타낼 수 있다. 엣지의 위치를 찾기 위해서는 그래디언트의 절댓값을 계산하고, 이 값이 임계값보다 큰 경우에 엣지로 정의한다. <br>

$$
	|\bigtriangledown{f}| = \sqrt{ (\frac{\sigma f}{\sigma x})^2 +  (\frac{\sigma f}{\sigma y})^2 }
$$

<hr>

#### 마스크 기반 엣지 검출
**소벨 마스크**
$$
\begin{bmatrix}
-1&0&1\\
-2&0&2\\
-1&0&1
\end{bmatrix} 
\begin{bmatrix}
-1&-2&-1\\
0&0&0\\
1&2&1
\end{bmatrix} 
$$
소벨 마스크를 이용하여 가로 방향과 세로 방향의 엣지를 검출한다. <br>
구현 방식은 다음과 같다.

~~~c++
EdgeSobel(img, imgEdge) {
	// imgEdge 초기화

	p1[][] = img.pixels
	p2[][] = imgEdge.pixels

	for(j from 1 to h-1)
	for(i from 1 to w-1) {
		// 세로, 가로 방향 마스크 연산
		h1 = 세로방향 소벨 마스크
		h2 = 가로방향 소벨 마스크
		// 그래디언트 크기 구하기
		hval = sqrt(h1*h1 + h2*h2)
		p2[j][i] = hval
	}
}
~~~

<hr>

#### 캐니 엣지 검출기
마스크 기반 엣지 검출기는 그래디언트 크기만을 이용하여 엣지를 검출한다. <br>
이 때문에 여러 픽셀들이 한꺼번에 엣지로 검출되는 단점이 있다. <br>
캐니 엣지 검출기는 중심 픽셀 하나를 엣지로 판별하는 알고리즘이다. <br>

캐니 엣지 검출기는 크게 네 개의 과정으로 구성된다. <br>
1. 가우시안 필터링
2. 그래디언트 계산(크기, 방향)
3. 비최대 억제
4. 이중 임계값을 이용한 히스테리시스 엣지 트래킹
<hr>

**그래디언트 계산** <br>
캐니 엣지 검출기는 그래디언트의 크기와 방향을 고려하여 정확하게 엣지를 검출한다. <br>
그래디언트 계산은 보통 소벨 마스크를 사용한다. <br>

$$
	|G| = \sqrt{G_x^2 + G_y^2} \\
	\theta = (\tan{\frac{G_y}{G_x}})^{-1}
$$
<br><br>

**비최대 억제** <br>
비최대 억제는 그래디언트 크기가 국지적 최대인 픽셀만을 엣지 픽셀로 설정하는 기법이다.
![canny](/images/canny.png){: width="80%" height="auto"}
캐니 엣지 검출기에서 비최대 억제를 적용하기 위해 그래디언트 방향 성분을 크게 네 개의 구역으로 나눈다. 밝은 색상의 픽셀이 그래디언트 크기가 큰 픽셀이고, 어두운 픽셀은 상대적으로 그래디언트 크기가 작은 픽셀이다. 국지적 최대를 검사할 때에는 엣지의 방향에 수직인 방향으로 인접한 픽셀과 크기를 비교한다. <br><br>

**이중 임계값을 이용한 히스테리시스 엣지 트레킹** <br>
![canny2](/images/canny2.png){: width="80%" height="auto"}
2개의 임계값을 사용하여 high보다 큰 그래디언트 값은 강한 엣지로 표현한다. low보다 크고 high보다 작은 픽셀은 약한 엣지로 본다. low보다 작은 픽셀은 엣지로 구분하지 않으며, 서로 연결되어있지 않은 엣지로 본다. 최종적으로 엣지로 선별되는 픽셀들은 굵은 실선으로 표현되어 있다. <br><br>

~~~c++
// sigma : 가우시안 필터 적용
// th_low, th_high : 이중 임계값
EdgeCanny(imgSrc, imgEdge, sigma, th_low, th_high) {
	// imgGaussian 초기화 & 가우시안 필터링
	FilterGaussian(imgSrc, imgGaussian, sigma)
	// 이미지 생성 및 초기화
	// imgGx : 세로방향 그래디언트 
	// imgGy : 가로방향 그래디언트
	// imgMag : Gx, Gy를 이용한 그래디언트 크기

	pGauss[][] = imgGaussian.pixels
	pGx[][] = imgGx.pixels
	pGy[][] = imgGy.pixels
	pMag[][] = imgMag.pixels

	for(j from 1 to h-1)
	for(i from 1 to w-1) {
		pGx[j][i] = 세로방향 소벨마스크 연산
		pGy[j][i] = 가로방향 소벨마스크 연산
		// 그래디언트 크기
		pMag[j][i] = sqrt(pGx[j][i]^2 + pGy[j][i]^2)
	}

	// imgEdge 생성 및 초기화
	pEdge[][] = imgEdge.pixels

	STRONG_EDGE = 255
	WEAK_EDGE = 128

	vector<point> strong_edges;

	for(j from 1 to h-1)
	for(i from 1 to w-1) {
		// 약한 엣지 보다 큰 경우만 국지적 검사
		mag = pMag[j][i]
		local_max = false;

		if(mag > th_low) {
			// 그래디언트 방향
			ang = atan2(pGy, pGx) * 180 / PI
			// 4개 구역 방향 검사
			if( 0도 검사 )
				// 중심 픽셀과 별표로 표시된 픽셀들의
				// 그래디언트 크기 비교
				if(mag >= pMag[j][i-1] && mag > pMag[j][i+1])
					local_max = true
			else if ( 45도 검사 )

			else if( 90도 검사 )

			else if ( 135도 검사 )
		}
		// 강한 엣지와 약한 엣지 구분
		if(local_max) {
			if( mag > th_high )
				pEdge[j][i] = STRONG_EDGE
				strong_edges.push_back(Point(i,j))
			else
				pEdge[j][i] = WEAK_EDGE
		}
		// 히스테리시스 엣지 트레킹
		while(!strong_edges.empty() {
			Point p = strong_edges.back()
			strong_edges.pop()

			x = p.x
			y = p.y

			CHECK_WEAK_EDGE ... 
		}
	}	
}
~~~

<hr>


### 직선 검출
#### 허프 변환을 이용한 직선 검출

### 포인트 검출
#### 해리스 코너 검출

### 컬러 영상처리

### 트루컬러 비트맵
#### 트루컬러 영상을 그레이스케일 영상으로 변환 

### 색상 표현 방법
#### RGB
#### HSI
색상, 채도, 명도

#### YUV
밝기, 색상, 색상

#### 색상 평면 나누기
#### 색상 평면 합치기
#### 컬러 엣지 검출

## 영상 분할
### 이진화 기법
#### 영상의 이진화
#### 반복적 기법

#### 고전적 레이블링
~~~c++
int IppLabeling8(IppByteImage& imgSrc, IppIntImage& imgDst, std::vector<IppLabelInfo>& labels){
    int w = imgSrc.GetWidth();
	int h = imgSrc.GetHeight();
    imgDst.CreateImage(w, h);
	BYTE** pSrc = imgSrc.GetPixels2D();
    int** pDst = imgDst.GetPixels2D();

    stack<IppPoint> points;
    int label_cnt = 0;

    int i, j;
    for(j=1; j<h-1; j++) {
        for(i=1; i<w-1; i++) {
			// 픽셀이 배경이거나 객체로 인식된 경우
            if(pSrc[j][i] != 255 || pDst[j][i] != 0) continue;
			
            label_cnt++;
            points.push(IppPoint(i,j));

            while(!points.empty()) {
                int x = points.top().x;
                int y = points.top().y;
                
                points.pop();
                pDst[j][i] = label_cnt;
				// 8방향 진행
                for(int ny = y-1; ny<= y+1; ny++) {
                    if(ny <0 || ny >= h) continue;
                    for(int nx = x-1; nx<=x+1; nx++) {
                        if(nx <0 || nx>=w) continue;
						// 주변 픽셀이 배경이거나 객체로 인식된 경우
                        if(pSrc[ny][nx] != 255 || pDst[ny][nx] != 0) continue;

                        points.push(IppPoint(nx, ny));
                        pDst[ny][nx] = label_cnt;
                    }
                }
            }
        }
    }

    //-------------------------------------------------------------------------
	// IppLabelInfo 정보 작성
	//-------------------------------------------------------------------------
	labels.resize(label_cnt);

	IppLabelInfo* pLabel;
	for (j = 1; j < h; j++)
	for (i = 1; i < w; i++)
	{
		if (pDst[j][i] != 0)
		{
			pLabel = &labels.at(pDst[j][i] - 1);
			pLabel->pixels.push_back(IppPoint(i, j));
			pLabel->cx += i;
			pLabel->cy += j;

			if (i < pLabel->minx) pLabel->minx = i;
			if (i > pLabel->maxx) pLabel->maxx = i;
			if (j < pLabel->miny) pLabel->miny = j;
			if (j > pLabel->maxy) pLabel->maxy = j;
		}
	}

	for (IppLabelInfo& label : labels)
	{
		label.cx /= label.pixels.size();
		label.cy /= label.pixels.size();
	}


	return label_cnt;
}
~~~
#### 외곽선 추적 기법
~~~c++
void IppContourTracing(IppByteImage& imgSrc, int sx, int sy, std::vector<IppPoint>& cp)
{
	int w = imgSrc.GetWidth();
	int h = imgSrc.GetHeight();

	BYTE** pSrc = imgSrc.GetPixels2D();

	// 외곽선 좌표를 저장할 구조체 초기화
	cp.clear();

	// 외곽선 추적 시작 픽셀이 객체가 아니면 종료
	if (pSrc[sy][sx] != 255)
		return;

	int x, y, nx, ny;
	int d, cnt;
	int  dir[8][2] = { // 진행 방향을 나타내는 배열
		{  1,  0 },
		{  1,  1 },
		{  0,  1 },
		{ -1,  1 },
		{ -1,  0 },
		{ -1, -1 },
		{  0, -1 },
		{  1, -1 }
	};

	x = sx;
	y = sy;
	d = cnt = 0;

	while (1)
	{
		nx = x + dir[d][0];
		ny = y + dir[d][1];

		if (nx < 0 || nx >= w || ny < 0 || ny >= h || pSrc[ny][nx] == 0)
		{
			// 진행 방향에 있는 픽셀이 객체가 아닌 경우,
			// 시계 방향으로 진행 방향을 바꾸고 다시 시도한다.

			if (++d > 7) d = 0;
			cnt++;

			// 8방향 모두 배경인 경우 
			if (cnt >= 8)
			{
				cp.push_back(IppPoint(x, y));
				break;  // 외곽선 추적을 끝냄.
			}
		}
		else
		{
			// 진행 방향의 픽셀이 객체일 경우, 현재 점을 외곽선 정보에 저장
			cp.push_back(IppPoint(x, y));

			// 진행 방향으로 이동
			x = nx;
			y = ny;

			// 방향 정보 초기화
			cnt = 0;
			d = (d + 6) % 8;	// d = d - 2 와 같은 형태
		}

		// 시작점으로 돌아왔고, 진행 방향이 초기화된 경우
		// 외곽선 추적을 끝낸다.
		if (x == sx && y == sy && d == 0)
			break;
	}
}
~~~

<br>

~~~c++
// 레이블링 후 외곽선 추적

IppByteImage imgContour(img.GetWidth(), img.GetHeight());
BYTE** ptr = imgContour.GetPixels2D();

int label_cnt = IppLabeling8(img, imgLabel, labels);
for(IppLabelInfo& info: labels) {
	std::vector<IppPoint> cp;
    IppContourTracing(img, info.pixels[0].x, info.pixels[0].y, cp);

    for(IppPoint pt: cp) {
    	ptr[pt.y][pt.x] = 255;

        pt.print();
    }
                    
}   
CONVERT_IMAGE_TO_DIB(imgContour); 

~~~




