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

아래 사진은 부수적인 설명을 위한 플레이어 오브젝트의 콜라이더 구성을 나타낸 것이다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/playerexplain.png?raw=true" width="600">
</center>



#### 점프, 이단 점프

플레이어의 점프와 이단 점프는 canjump, candoublejump라는 boolean 형태의 변수로 관리된다. 땅에 닿아 있을 때 canjump가 true형태로 존재하며, 점프 키를 눌렀을 때 false로 변환한다. 동시에 candoublejump를 활성화하고, 점프 키를 눌렀을 때 doublejump 함수를 호출한다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/doublejump.gif?raw=true" width="600">
</center>

```c#
if (Input.GetKeyDown(KeyCode.LeftAlt))
{
    if (canjump == true) jump();
    else if (candoublejump == true) doublejump();
}
```



#### 밧줄 이동

플레이어가 밧줄을 탈 때, 전용 애니메이션을 호출하며, 밧줄을 타고 상하로 이동이 가능하여야 한다. 이를 위하여 우선 맵 오브젝트에서, 밧줄들에 별도로 트리거 형태의 콜라이더를 달아주었다. 이를 통해 플레이어가 밧줄과 겹친 상태로 위 방향키를 눌렀을 때 플레이어에게 적용되어 있는 중력효과를 제거하고, 애니메이션을 강제로 밧줄을 타는 모션으로 전환합니다.

플레이어가 밧줄을 타는 도중에는, 상단의 플랫폼으로 이동하기 위해서, 상단의 플랫폼과 충돌하지 않고 통과하여야 한다. 이를 위해 플레이어의 머리부분에 원형의 콜라이더를 추가하여, 이 콜라이더와 플랫폼이 접촉할 경우 플레이어의 콜라이더를 트리거로 변경하도록 설정하였다.{:.note}

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/rope_underjump.gif?raw=true" width="600">
</center>



#### 하단점프

플레이어가 아래 방향키를 누른 채로 점프키를 입력하면, 현재 서있는 플랫폼을 통과하여, 아래 플랫폼에 착지하여야 한다. 이를 위해서, 아래 방향키와 점프키가 같이 입력되면, 플레이어의 콜라이더를 트리거로 변경한다. 이후 낙하상태에서, 플레이어의 상단에 있는 원형 콜라이더가 플랫폼을 통과하면, 다시 플레이어의 콜라이더를 작동시키고, 아래 플랫폼은 통과하지 않고 착지하게 한다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/underjump.png?raw=true" width="1000">
</center>



 ### 플레이어 스킬

메이플스토리에서 캐릭터의 스킬은 사용 목적에 따라 주력기, 사냥기, 유틸리티 등으로 구분된다. 이 중 주력이라고 생각되는 부분들을 시작으로 일부 스킬들을 구현하였다.



#### 주력기

캐릭터를 궁수 직업군으로 설정하였기에, 주력기는 속사기로 설정하였다.

속사기는 키다운 형식의 스킬로, 스킬키를 누르고 있는 동안 연속해서 화살을 발사하는 스킬이다.{:.note}

키다운 동안 애니메이션을 반복해야 하고, 반복하는 동안 지속적으로 화살을 발사해야 한다. 이를 구현하기 위해, 속사기 자체를 하나의 오브젝트로 제작하였다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/playerstructure.png?raw=true" width="300">
</center>
스킬키를 누르고 있을 때 스킬 오브젝트(rapidfire_body)를 활성화하며, 이 오브젝트는 일정 시간 단위로 화살을 발사한다. 발사된 화살은 다시 별개로 오브젝트로 취급되며, 1초간 날아간 뒤 소멸한다.

스킬 오브젝트가 플레이어의 하위 오브젝트여서, 플레이어가 스킬을 사용하는 중에 바라보는 방향을 전환하면, 스킬이펙트가 같이 방향을 전환하게 된다. 이 때, 이미 발사된 화살과 나비는 플레이어의 방향에 영향을 받이 않아야 한다. 따라서, 플레이어의 자식으로 화살 객체가 생성되는 것이 아니라, 별도의 객체로 생성하였다. 아래는 객체들이 생성되는 구조이다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/playerstructure2.png?raw=true" width="300">
</center>

아래 사진은 구현된 주력기 스킬의 테스트 사진이다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/rapid2.gif?raw=true" width="600">
</center>



#### 사냥기

사냥기는 비교적 넓은 범위에 있는 다수의 몬스터를 공격하는 기술이다. 실제 메이플스토리의 루시드 보스몬스터의 패턴에서 에셋을 추출하여 사용하였다. 아래는 추출한 이미지이다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/flower_bomb_full.png?raw=true" width="800">
</center>

위 이미지에서 하나의 꽃을 추출하여 정리하였으며, 이를 여러 색깔로 칠하여 사용할 에셋을 완성하였다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/flower_bomb.png?raw=true" width="800">
</center>

스킬은 꽃이 핀 후, 터지는 듯한 느낌으로 제작하였으며, 3개의 큰 꽃과, 9개의 작은 꽃으로 구성된다. 스킬을 사용하는 데에 있어 지루함을 줄이기 위해, 이 스킬에 사용되는 꽃의 색깔과 위치를 랜덤하게 생성되도록 하였다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/flowerbomb.gif?raw=true" width="600">
</center>

기본적으로는 플레이어를 기준으로 대각선 위 방향으로 꽃이 생성되며, 처음 생성되는 보라색 꽃을 기준으로, 일정 범위 안에서 랜덤한 위치에, 랜덤한 색으로 꽃들이 생성되게 된다. 일정한 시간 간격을 두고 생성된 꽃들은 다시 일정한 시간 간격으로 폭발하게 되며, 몬스터에게 대미지를 줄 수 있다.

꽃이 생성되는 위치가 일정하지 않기 때문에, 이 스킬의 히트박스는 꽃에게 적용할 수 없었다. 따라서 이 스킬의 히트박스는 생성되는 에셋과는 별개로 작성되었다. 히트박스에도 애니메이션을 적용하여 작은 히트박스가 점점 커지며 범위 안에 포함되는 몬스터를 타격한다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/flowerbomb_animation.png?raw=true" width="600">
</center>

아래 사진에서, 초록색 사각형(스킬의 콜라이더)가 확장되는 것을 단편적으로 확인하고 있다.

<center>
	<img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/flowerbombtest.gif?raw=true" width="600">
</center>



#### 사출기

스킬로 몬스터에게 타격을 가할 때, 일정 확률로 적을 추가 타격할 수 있는 나비가 사출된다. 

이 나비를 사출하는 메서드는 몬스터에게 포함되어 있다. 몬스터가 스킬에 피격되었을 때, 플레이어에게로 몬스터 정보를 전달한다. 당시에 이렇게 코드를 작성한 이유는, 생성된 나비가 몬스터를 향해 날아가기 위해 몬스터의 위치 정보가 필요하기 때문이다.<br>하지만, 코드의 독립성적인 측면에서 보았을 때, 이는 명백히 옳지 않은 구조이다. 지금 다시 개발한다면, 이 나비를 사출하는 메서드를 스킬이펙트 인터페이스에 포함하고, 각종 스킬이펙트에서 상속받아서 사용하게 제작할 수 있을 것 같다.{:.note}

나비는 사출되면, 플레이어의 상하로 퍼지게 된다. 한 번 강한 힘을 받아 사출된 뒤 속도가 느려지고, 일정 시간 후에 몬스터를 향해 직선으로 날아가게 된다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/wim_final.gif?raw=true" width="600">
</center>

만약 나비가 사출되었으나, 타겟인 몬스터가 죽어서 존재하지 않는 상태가 되면, 나비는 자동으로 사라지도록 설계하였다. 이 부분에서 나비가 사라지지 않고 새로운 몬스터를 찾는 것도 고려하였지만, 수십 개의 나비가 한번에 find메서드를 사용하는 것이, 자원 효율적인 츠면에서 매우 좋지 않다고 생각하여 이처럼 처리하게 되었다.



#### 특수자원

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/resource.gif?raw=true" width="600">
</center>





#### 자연스러운 움직임.

실제 스킬을 구현하고 적용하는 과정에서, 스킬들에 다양한 모션을 적용해보며 가장 적절하다고 생각하는 모션을 선정하였다.



##### 주력기

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/rapid1.gif?raw=true" width="400">
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/rapid2.gif?raw=true" width="400">
</center>

위 사진의 왼쪽(위쪽)은 최초 구현하였던 주력기 스킬의 모션이다. 화살과 나비가 하나의 에셋으로 묶여 날아가도록 설계하였지만, 실제 적용해보았을 때 굉장히 어색한 움직임을 보이는 것을 확인하였다.

이를 오른쪽(아래쪽) 사진처럼 개선하였다. 나비와 화살을 분리하고, 나비의 경우 생성될 때는 느린 속도로 출발하여, Addforce를 이용하여 가속되도록 만들었다.



##### 사출기

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/wim1.gif?raw=true" width="400">
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/wim2.gif?raw=true" width="400">
</center>

위 사진의 왼쪽(위쪽)은 최초 구현하였던 사출기 스킬의 모션이다. 푸른색 나비가 포물선 모양을 그리며 적에게 향하는 것을 볼 수 있다. 이를 유저 커뮤니티에 게시하였을 때, 나비의 움직임이라기에는 부자연스럽다는 의견이 많았다. 



### 오브젝트 풀링

스킬을 사용하는 데에 있어서 화살, 나비 등의 오브젝트는 반복적으로 생성되고 삭제된다. 이런 메모리 점유 및 릴리즈를 세임이 진행되는 동안 반복하는 것은 **외부 메모리 단편화** 현상을 일으키게 된다. 이를 해결하기 위해 오브젝트 풀링을 적용하였다.

오브젝트 풀링은 게임 실행 중 반복적으로 사용될 오브젝트를 미리 생성해둔 뒤, 필요할 때 가져와서 사용하고, 사용이 끝나도 삭제하지 않고 보이지 않게만 숨겨두어, 항상 정해진 위치에 정해진 크기의 메모리만을 사용하게 하는 방식이다.

이 게임에서 주력기의 화살과 나비, 사냥기, 사냥기의 꽃, 사출기에 적용되었다. 주력기를 기준으로 설명하면, 주력기의 화살의 경우 0.1초마다 한번씩 발사되고, 각각의 화살은 1초간 진행한 뒤 소멸되므로, 10개의 화살을 만들어두면 부족하지 않게 화살을 불러오며 사용할 수 있다. 이에 각종 상황에 의해 생길 수 있는 딜레이를 생각하여, 총 11개의 화살을 만들어 두고 사용하게 하면, 모종의 이유로 화살이 발사되지 않는 상황도 방지할 수 있다.

아래 사진은 유니티엔진 상에서 화살 오브젝트가 활성, 비활성화 되는 것을 테스트해본 것이다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/objectpulling.gif?raw=true" width="600">
</center>



### 몬스터

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/monster.gif?raw=true" width="600">
</center>



## Assets

게임 구현에 필요한 에셋들은, 일부 메이플스토리의 클라이언트를 분해하여 가져오거나, 직접 그려서 사용하였다.

아래 그림은 캐릭터의 스킬에 사용된 에셋으로, 직접 그린 에셋이다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/rapidfireeff1.gif?raw=true" width="350">
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/rapidfireeff2.gif?raw=true" width="350">
</center>

아래 그림은 메이플스토리의 클라이언트에서 



## 한계

### 프로젝트 구조

이 프로젝트는 각 객체별로 스크립트가 분리되어 있고, 이에 따라 동작하지만 그 경계가 매우 모호하게 설정되어있다.



### 지금 다시 개발한다면

- 스킬의 경우, 하나의 인터페이스를 만들어 모든 스킬에 상속시킨다.
  - 이렇게 제작할 시, 몬스터 등 스킬의 피격을 처리해야 할 때, 스킬 인터페이스를 넘겨받아 한번에 데이터를 처리할 수 있게 된다.



## 추가

구현 과정에서 발생했던 버그들 중 일부와, 그 원인에 대해 적고자 한다.



나비 무한 사출 문제.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/mechanicbug.gif?raw=true" width="700">
</center>

특수자원이 다 채워졌을 때 사출되는 붉은 나비가 무한이 사출된다. 이 버그는 붉은 나비가 특수자원을 채워주기 때문에 발생한 문제이다. 테스트를 위해 특수자원이 빠르게 채워지도록 설정해 두었는데, 이 때문에 붉은 나비가 피격했을 시 특수자원이 400% 이상 채워지게 되어 사출되었던 것의 4배의 붉은 나비가 사출되게 된다.이 과정이 반복되어 무한이 나비가 사출되는 문제가 발생하였다.



스킬이펙트 반복 문제.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Lucid/reverseshooting.gif?raw=true" width="700">
</center>

오브젝트풀링을 구현하는 과정에서 발생한 문제이다. 한 번 생성된 오브젝트가 소멸될 때 사라지지 않고, 반복해서 생성되는 문제가 발생하였다.

