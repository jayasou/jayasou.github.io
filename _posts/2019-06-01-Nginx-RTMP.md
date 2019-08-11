---
layout: post
title: CentOS7 - Nginx - rmtp-module 설치
comments: true
categories: [Web]
---

##  nginx 다운로드

~~~
$ wget http://nginx.org/download/nginx-1.7.5.tar.gz
~~~

## nginx-rtmp-module 다운로드

~~~
$ wget https://github.com/arut/nginx-rtmp-module/zipball/master
~~~

## /usr/local/scr 폴더에 압축 해제

~~~
$ tar -zxvf nginx-1.7.5.tar.gz
$ unzip master.zip
~~~

## Build 하기 

압축을 해제하고 나면 nginx-1.7.5 폴더가 생성된다. 이 폴더 안으로 들어가면 configure 이라는 파일이 있다. 이 파일을 다음과 같은 명령어로 실행시키자.

~~~
$ ./configure --add-module=/usr/local/scr/nginx-rtmp-module --with-debug
~~~

이 명령을 실행시킬 때 master.zip 파일을 압축해제 하고 난 nginx-rtmp-module 위치를 적어준다. 나는 압축해제 할 당시 폴더 명이 'arut-nginx-rtmp-module-791b613' 와 같이 생성되었는데 'nginx-rtmp-module'로 바꿔주었다. 경로는 '/usr/local/scr/nginx-rtmp-module' 이렇게 되기 때문에 이 경로를 명령어 뒤에 적어준 것이다.

하지만 이렇게 했을 때 error 메시지를 만날 수도 있다. 
나는 다음과 같은 메시지를 보게 되었다.

~~~
error --with-http_ssl_module', exiting and error: SSL modules require the OpenSSL
~~~

자, 이럴 때는 빌드명령어를 다음과 같이 적어준다.

~~~
$ ./configure --add-module=/usr/local/scr/nginx-rtmp-module --with-debug --with-http_ssl_module
~~~

처음 적었던 빌드 명령어 뒤에 '--with-http_ssl_module' 이 명령어가 추가되었다. 하지만 또 똑같은 error 메시지를 보게 된다면, 다음과 같이 openssl 패키지를 설치하자.

~~~
$ yum install -y openssl-devel
~~~

다시 한번 두번째 빌드 명령어를 입력해보자. 빌드가 잘 되었는가? 

~~~
$ make
$ make install
~~~

make 명령어는 설치하려고 하는 패키지의 모든 실행 파일들을 컴파일한다.
make install 은 파일들을 적당한 디렉토리에 설치한다.
이 명령어를 수행하고 나면 /usr/local/nginx라는 폴더가 생긴다. 수행하고 난 뒤 결과에서 생성된 파일들이 나와있으니 확인해보자.


## nginx.conf 설정파일을 들여다보자

이렇게 만들어진 nginx 의 설정파일은 그냥 nginx 를 설치했을 때와 다른 설정들이 담겨져있다. 필자는 다음과 같이 생성되었다. 
아차! 이 설정파일은 '/usr/local/nginx/conf' 폴더 밑에 nginx.conf 파일이다. 설치 후 생성된 nginx 폴더 아래에 있다는 것을 주의하자!

~~~
#user  nobody;
worker_processes  auto;
events {
    worker_connections  1024;
}

# RTMP configuration
rtmp {
    server {
        listen 1935; # Listen on standard RTMP port
        chunk_size 4000;

        application show {
            live on;
            # Turn on HLS
            hls on;
            hls_path /mnt/hls/;
            hls_fragment 3;
            hls_playlist_length 60;
            # disable consuming the stream from nginx as rtmp
            deny play all;
        }
	
	application vod {
		play /usr/local/nginx/html;
	}
    }
}

http {
    sendfile off;
    tcp_nopush on;
    aio on;
    directio 512;
    default_type application/octet-stream;

    server {
        listen 8080;

        location / {
            # Disable cache
            add_header 'Cache-Control' 'no-cache';

            # CORS setup
            add_header 'Access-Control-Allow-Origin' '*' always;
            add_header 'Access-Control-Expose-Headers' 'Content-Length';

            # allow CORS preflight requests
            if ($request_method = 'OPTIONS') {
                add_header 'Access-Control-Allow-Origin' '*';
                add_header 'Access-Control-Max-Age' 1728000;
                add_header 'Content-Type' 'text/plain charset=UTF-8';
                add_header 'Content-Length' 0;
                return 204;
            }

            types {
                application/dash+xml mpd;
                application/vnd.apple.mpegurl m3u8;
                video/mp2t ts;
            }

            root /mnt/;
        }
    }
}
~~~
## nginx 실행시키기

nginx 실행파일은 '/usr/local/nginx/sbin' 아래에 nginx 로 되어있다. 다음의 명령어로 실행시켜보자.

~~~
$ ./nginx
~~~

잘 실행이 되었는가 ? 필자는 다음과 같은 결과 메시지를 받게 되었다.

~~~
[emerg]: unknown directive "aio ...
~~~

aio 모듈을 설치하지 않았다. 나에겐 문제가 될게 없었다. 아직은 이 모듈을 사용할 때가 아니다.  다시 nginx.conf 로 가서 수정하였다. 

~~~
aio on;
~~~

~~~
# aio on;
~~~

언젠간 저 모듈을 공부하고 사용할 날이 올 수 있으니 앞에 # 으로 주석처리만 해주었다.
다시 nginx를 실행시키자

~~~
$ netstat -antu
~~~

위의 명령어를 사용해서 nginx가 어떤 포트들을 이용하는지 확인하자.
1935, 8080 포트를 이용하는 것을 알 수 있다.

## 비디오 스트리밍
진짜 스트리밍을 해볼까
다시 nginx.conf 를 들여다 보면 rtmp 안의 server 는 1935 포트를 사용한다. 그 안의 application 내용을 보면 hls 경로가 /mnt/hls/라고 되어 있다. 이 폴더 아래에 자신이 스트리밍 하고 싶은 혹은 전달하고 싶은 파일들을 넣어두면 된다. 경로를 바꾸고 싶다면은 바꾸는 것도 가능하다.
경로 변경은 다음에 하기로 하고 '/mnt/hls/' 폴더 안에 예전에 찍어둔 동영상을 파일질라를 사용해서 넣어봤다. 동영상 이름은 'KPLJ.mp4' 이다. 
브라우저에 다음과 같이 입력해보자.

~~~
http://localhost:8080/KPLJ.mp4
~~~

이러면 다운로드가 된다.
이 url 은 어디서 쓸까..
앞으로 고민해봐야겠다. 친구들과 같이 찍은 동영상들이 많은데 같이 공유할 수 있게 블로그를 만들어볼까 생각중이다. 
