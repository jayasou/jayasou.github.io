---
layout: post
title: Docker 시작하기 
comments: true
categories: [Web]
---

### 우리의 이사
> 초기에 이사를 갈 때는 수레에 짐을 실어서 옮겼다. 수레로 이사를 하는 시대가 지나고 트럭에 물건들을 채워서 이사를 하기 시작했다. 이제는, 박스에 물건들을 담아서 트럭으로 옮기거나 배송을 한다.
박스 !
이 박스를 우리는 '컨테이너' 라고 한다.

### 클라우드
> 서버 시장은 급속도로 클라우드 환경으로 옮겨진다. 기존의 물리적 서버 설치 및 환경 설정을 하던 방식과는 다르게 간단한 클릭만으로 가상 서버를 만들 수 있게 되었다. 우리는 서비스 자체를 하나의 컨테이너로 만들 수 있게 되었다. 서비스의 환경을 변경시키지 않고도 컨테이너로 만들어 이사를 할 수 있게 된 것이다.

### 도커
> 도커는 이런 서비스 환경을 하나의 이미지로 생성하여 배포하는 일을 한다. 이런 이미지를 Git과 같이 버전 관리 기능 또한 제공한다. 
도커의 이미지는 16진수로 된 ID로 구분한다. 만약 이미지가 변경되었을 시 이미지를 통째로 재생성하지 않고 바뀐 부분만 생성한 뒤 부모 이미지를 참조한다. 
이를 '레이어' 라고 한다. 

# Docker 시작하기
#### 도커의 명령어는 항상 root 권한으로 실행하자
~~~
$ sudo su
~~~
#### search : 이미지 검색하기
> 보통 ubuntu, centos, redis 등 OS나 프로그램 이름을 가진 이미지가 공식 이미지
~~~
$ docker search centos
~~~

#### pull : 이미지 받기
~~~
$ docker pull centos:latest
~~~

#### images : 이미지 목록 출력
~~~
$ docker images
~~~

#### run : 컨테이너 생성
~~~
$ docker run -it --name startUbuntu ubuntu /bin/bash
~~~
> docker run <옵션> <이미지 이름> <실행할 파일>
> 우분투 이미지를 컨테이너로 생성한 뒤 이미지 안의 /bin/bash 를 실행

#### ps : 컨테이너 목록 확인
~~~
$ docker ps
$ docker ps -a
~~~

#### start : 컨테이너 시작 / restart, stop
~~~
$ docker start startUbuntu
~~~

#### attach : 컨테이너에 접속
~~~
$ docker attach startUbuntu
~~~
> /bin/bash 명령으로 실행했기 때문에 명령을 자유롭게 입력할 수 있다.

#### exec : 외부에서 컨테이너 안의 명령 실행
~~~
$ docker exec startUbuntu echo "Hello World"
~~~
> docker exec <컨테이너 이름> <명령> <매개 변수>
> 컨테이너 안의 echo 명령을 실행했기 때문에 매개 변수인 Hello World가 출력된다.

#### rm : 컨테이너 삭제
~~~
$ docker rm startUbuntu
~~~

#### rmi : 이미지 삭제
~~~
$ docker rmi ubuntu:latest
~~~
> docker rmi <이미지 이름> : <태그>
