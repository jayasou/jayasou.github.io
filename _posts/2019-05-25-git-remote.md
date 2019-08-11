---
layout: post
title: Git 공부하기 
comments: true
categories: [Web]
---

###  remote 명령어로 저장소 확인하기

~~~bash
$ git remote
~~~

~~~bash
$ git remote -v
~~~

> -v 옵션을 주어 URL 을 확인할 수 있다.

~~~bash
$ git remote show origin
~~~

> 리모트 저장소의 구체적인 정보 확인
> 이 명령어는 git pull 명령을 실행 할 때 master 브랜치와 merge할 브랜치가 무엇인지 보여준다.
> git pull 명령은 remote 저장소의 브랜치의 데이터를 모두 가져오고 나서 자동으로 merge 한다.
> >Fetch & Pull
> > git fetch : 코드를 받아온다.
> > git pull :  코드를 받아와서 merge 한다.
> > fetch 를 이용할 경우 merge 를 하지 않아서 충동할 위험이 적지만 pull 명령어를 사용할 경우 충돌위험이 있다. 이때 충동점을 해결해주고 commit 해준 뒤에 pull을 하면 된다.

### checkout 명령어

~~~bash
$ git checkout HEAD~1
~~~

> 한단계 전 커밋으로 돌려 준다. 이때 숫자를 바꾼 만큼 숫자만큼의 전단계로 돌아간다.

~~~bash
$ git checkout HEAD~10
~~~

~~~bash
$ git checkout master
~~~

> master branch 로 돌아온다. 여러 브랜치를 관리하고 있다면 돌아가고 싶은 브랜치명으로 입력하면 된다.

### Git 익숙해지기
git 에 대해서 모르는 점들이 많았었던 것 같다. 이런 정리를 해놓는 이유는 혼자서 장난감삼아 만들어보는 프로젝트에 git을 사용하는데 많은 시행착오가 있었기 때문이다. 

![2019-06-13](/images/2019-06-13.png)

꺽임이 아름다운 형태로 나타나게 되었다. 어려움을 겪고 나니까 어느정도의 git에 대한 기본적인 기능과 구조를 공부하게 되었고 git의 편리함에 놀라게 되었다. 앞으로도 git을 사용하고 더 잘 가지고 놀 수 있으면 좋겠다.
