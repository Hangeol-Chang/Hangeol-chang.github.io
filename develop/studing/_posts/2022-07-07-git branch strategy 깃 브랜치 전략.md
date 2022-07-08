---
layout: post
title: 라이브러리와 프레임워크의 차이점.
hide_last_modified: true
---

0. toc

{:toc}



# 깃 브랜치 전략

## Git-flow

Git-flow는 브랜치를 크게 4가지로 나누어 개발하는 전략.

- 메인 브랜치(Main branch)
- 피처 브랜치(Feature branch) or 토픽 브랜치(Topic branch)
- 릴리스 브랜치(Release branch)
- 핫픽스 브랜치(Hotfix branch)



master 브랜치와 develop 브랜치를 중심으로 사용하며, feature, release, hotfix 브랜치는 머지 후 삭제하는 식으로 사용합니다.

주기적으로 배포해야 하는 프로젝트에 적합.

<img src="https://lh5.googleusercontent.com/YBoig12xGUyZKF3j9lNrPT_vuDqLvh2Y4snSSBL0UuILhBxvfJjXZRkhUTjmEQIJ_31GkQnsb5fTI3nSwmL8zAi3ZrpPm_QARYF1Q9IyooXdXL8CWiDxIyCl32JWmwFBL5J0Igli" >



### Main Branch

master, develop 두 브랜치를 메인 브랜치로 사용합니다.



#### Master

- 배포 가능한 상태만을 관리하는 브랜치.
- 이 브랜치네서는 작업을 하지 앟고, 완성된 버전을 관리하는 용도로 사용.

#### Develop

- 실제 개발의 토대가 되는 브랜치.
- 이 브랜치를 이용하여 다양한 브랜치로 뻗어나가, 기능을 제작하게 된다.
- feature 브랜치에서 개발된 기능들을 통합하는 역할.



### Supporting Branch

feature, topic 등의 브랜치.

develop로부터 분기된 브랜치이다.



#### feature

- develop로부터 생성된 브랜치로, 실제 기능을 개발하는 브랜치
- Git-flow 전략에서 지칭하는 단위 개발 브랜치.
- 기능을 완성할 때 까지 유지하고, 완성되면 develop로 merge한다.

#### release

- QA(품질 보증)을 위해 develop에서 생성하는 브랜치.
- 배포를 위한 최종적인 버그 수정 등의 개발을 수행.
- 배포 가능한 상태가 되면 master 브랜치로 병합시키고, 출시된 master 브랜치에 버전 태그를 추가한다.
- release에서 버그 수정 시, 이를 develop로도 적용해 줘야 함.

#### hotfix





## Github-flow

<img src="http://cdn-ak.f.st-hatena.com/images/fotolife/s/shoma2da/20151104/20151104223339.png">

<img src="https://subicura.com/git/assets/img/github-flow.2fafce92.png">



Git flow가 github에서 사용하기에 복잡하다고 생각하여 개발된 이론입니다.

hotfix, feature 브랜치를 구분하지 않으나, 우선순위를 두어 관리합니다. 수시로 배포가 일어나며, CI와 배포가 자동화되어있는 프로젝트에 사용됩니다.



### Master Branch

master 브랜치는 언제든 배포가 가능하여야 한다.

그렇게 때문에 mater에 다른 브랜치를 merge할 때는, 충분한 테스트가 이루어져야 하며, 로컬이 아닌 실제 서버 환경에서 이루어져야 한다.



### Branch

git-flow방식과 다르게 이름 규약 등이 존재하지 않고 자유롭다.

따라서, 팀원 간의 원활한 소통을 위해 브랜치명을 명확하게 작성하고, 커밋 메세지 또한 상세하게 작성한다.



#### 수시 push

git flow의 경우, 하나의 기능을 개발하는 사람들끼리 local에서 코드를 공유하고, 완성된 기능을 push 하는 식으로 작업하지만, github flow의 경우 수시로 push하는 것을 기본으로 한다. 자신이 하고 있는 일을 타인이 보게 한다.



#### Pull request

merge를 임의로 실행하는 것이 아니라, pull request를 통해 작업한다. 이를 통해 여러 사람이 코드를 검토하고, master브랜치로 반영하게 된다.



master 브랜치로 머지되었을 때 즉시 배포되어야 한다.
