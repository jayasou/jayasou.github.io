---
layout: post
title: Jenkins 맛보기3 
comments: true
categories: [Web]
---

# jenkins 아이템 등록하기
## 새로운 Item
아이템은 하나의 프로젝트라고 생각하시면 됩니다. 저는 jenkins에 등록된 프로젝트를 자동빌드, 배포가 되도록 설정해놓았습니다. 아이템을 등록하기 위해서 메뉴 상단에 새로운 Item을 클릭합니다.
![jenkins-new-item](/images/jenkins-new-item.png)
이런 페이지가 나오게 됩니다. 제일 위에 있는 Freestyle project를 클릭하시고 생성할 프로젝트 명을 적어주시면 됩니다. 저는 github의 프로젝트명과 동일하게 작성하였습니다. 
## 프로젝트 구성
#### 소스코드관리
여기까지 하게 된다면 생성한 아이템의 구성을 설정할 수 있습니다. 스크롤을 쭉쭉 내리시거나 상단바에 있는 소스코드관리를 클릭하게 되면 Git과 연동을 할 수 있습니다. 앞서 준비해놓았던 것들이 빛을 발합니다. 여기 Credentials 를 보시면 설정해놓았던 Credential의 ID가 드롭박스에 나타나게 됩니다. 그리고 Repository URL 에는 github 저장소의 ssh 주소를 적어주시면 됩니다. branch도 다른 것을 만들지 않았기 때문에 저는 master로 그대로 두었습니다. 
![jenkins-git-ssh](/images/jenkins-git-ssh.png)
git의 ssh 주소는 자신의 github 주소아래 프로젝트에서 가져올 수 있습니다.
![jenkins-소스코드관리](/images/jenkins-소스코드관리.png)
저의 소스코드관리 설정은 위와 같은 모습으로 되어있습니다.
#### 빌드유발
그 다음으로는 git 저장소에 push 할 때마다 hook 을 일으키기 위한 설정입니다.
![jenkins-빌드유발](/images/jenkins-빌드유발.png)
이렇게 빌드유발에서 설정하시면 됩니다.
#### 빌드
마지막으로는 빌드설정을 합니다. 여기서 빌드는 어떻게 해야할까요. 많은 방법들이 있을 겁니다. 하지만 저는 아직 아주아주 초급자이기 때문에 심화된 방법을 사용하지 못합니다. 부끄럽기도 하지만 기록을 해두기 위해 글을 남겨보자면 ...
저의 git 저장소에는 maven으로 package한 target폴더가 들어있습니다. 물론 중요한 설정파일은 빼놓았습니다. 이때 target폴더 아래에 jar 파일이 생성이 됩니다. 이 target폴더의 jar파일을 빌드합니다. 순서는 다음과 같습니다.
1. Git push
2. Webhook
3. Fetching From Git 
4. SSH(GCP) connect -> Transfer File (.jar)
5. Build
<hr>
결론적으론 설정은 다음과 같습니다.

![jenkins-빌드](/images/jenkins-빌드.png)

> 여기서 저는 왜 shell 명령어를 id라고 했을까요
