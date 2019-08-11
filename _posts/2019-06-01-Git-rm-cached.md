---
layout: post
title: Git rm --cached
comments: true
categories: [Web]
---

# 로컬에서  파일 삭제하고 push 하면 원격 저장소에는 그대로 남아있다.

Git 새내기인 나는 아무것도 몰랐다. 그대로 남아있을 줄이야
원격저장소의 파일을 삭제하는 방법을 알아보자. 로컬에서 아무리 삭제하고 push 를 한들 원격저장소에는 push 한 내용이 그대로 남아있다. 다음과 같은 명령어를 사용해보자

~~~bash
$ git rm --cached <fileName>
~~~

이렇게 적어주면 캐시 파일이 사라진다. 하지만 이 명령어만 입력한다고 해서 지워지지 않는다.
반드시 commit 하고 push 하자
로컬에 있는 파일을 지우는 명령어도 있다.

~~~bash
$ git rm <fileName>
~~~

위의 명령어를 입력하면 로컬에서 파일이 지워진다. 하지만 .. 잘 쓰진 않을것 같다. 
