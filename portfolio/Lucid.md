---
layout: page
title: Lucid
description: >
  detail description about Lucid Project
---



0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## About 

이 페이지는 개인프로젝트로 진행하였던 게임 제작 프로젝트 **Lucid**에 대해 소개하고 있습니다. 게임 진행을 위한 로직들에 대한 설명과, 이를 구현하는 과정에서 발생하였던 중요 이슈 및 기타 사항들을 포함하고 있습니다.



* [develop_youtube]{:.heading.flip-title} --- 개발과정 유튜브 링크
  {:.related-posts.faded}

[develop_youtube]: https://www.youtube.com/watch?v=1ewPsJVkCc4&amp;list=PLghOZn1LvD2NCw7RdcUJKq5gSYtNWu4vo



## Abstract

이 게임은 메이플스토리의 팬게임이다. 메이플스토리를 즐기던 중, 보스몬스터 '루시드'의 스토리를 보게 되었고, 매력적인 캐릭터라고 생각하게 되었다. 충분히 플레이어블 캐릭터로 출시될 가능성이 보이고, 신캐릭터 관련 루머가 돌면 빠지지 않고 말이 나오는 캐릭터지만, 아직 플레이어블 캐릭터로 출시되지 못했다.

이에 **'루시드가 플레이어블 캐릭터로 나온다면 어떤 모습일까?'** 라는 컨셉으로 개발을 시작하게 되었다. 실제 메이플스토리 캐릭터들의 스킬 구성을 분석하고, 이와 비슷한 밸런스를 제공하는 플레이어블 캐릭터를 만드는 것을 목표로 하였다.



## 기술 설명

### 플레이어 이동

기본적으로 플레이어는 좌우 이동, 점프, 이단 점프, 하단 점프, 밧줄 오르내리기가 가능하다.



#### 점프, 이단 점프

플레이어의 점프와 이단 점프는 canjump, candoublejump라는 boolean 형태의 변수로 관리된다. 땅에 닿아 있을 때 canjump가 true형태로 존재하며, 점프 키를 눌렀을 때 false로 변환한다. 동시에 candoublejump를 활성화하고, 점프 키를 눌렀을 때 doublejump 함수를 호출한다.

```c#
if (Input.GetKeyDown(KeyCode.LeftAlt))
{
    if (canjump == true) jump();
    else if (candoublejump == true) doublejump();
}
```



#### 밧줄 이동

플레이어가 밧줄을 탈 때, 전용 애니메이션을 호출하며, 밧줄을 타고 상하로 이동이 가능하여야 한다. 이를 위하여 우선 맵의 



#### 하단점프, 플랫폼 간 이동



 ### 플레이어 스킬



#### 자연스러운 움직임.

그 속사기랑 사출기 나비 움직임 최적화한거



## Assets



## 한계

### 프로젝트 구조

이 프로젝트는 각 객체별로 스크립트가 분리되어 있고, 이에 따라 동작하지만 그 경계가 매우 모호하게 설정되어있다.



### 지금 다시 개발한다면

- 스킬의 경우, 하나의 인터페이스를 만들어 모든 스킬에 상속시킨다.
  - 이렇게 제작할 시, 몬스터 등 스킬의 피격을 처리해야 할 때, 스킬 인터페이스를 넘겨받아 한번에 데이터를 처리할 수 있게 된다.
