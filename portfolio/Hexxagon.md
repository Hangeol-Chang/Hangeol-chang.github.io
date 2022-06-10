---
layout: page
title: Hexxagon
description: >
  detail description about Hexxagon Project
---



0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## About

이 페이지는 개인프로젝트로 진행하였던 게임 제작 프로젝트 **Hexxagon**에 대해 소개하고 있습니다. 게임 진행을 위한 로직들에 대한 설명과, 이를 구현하는 과정에서 발생하였던 중요 이슈 및 기타 사항들을 포함하고 있습니다.



* [develop_youtube]{:.heading.flip-title} --- 개발과정 유튜브 링크
  {:.related-posts.faded}

[develop_youtube]: https://www.youtube.com/watch?v=jfQ35B7fGc0&list=PLghOZn1LvD2No6V2XVAvCAHQgyeea9G43

## Abstract

Hexxagon은 1993년 개발된 보드게임으로, 크게 유명한 게임은 아니지만, 현재까지 형태를 유지해 오고 있다. 실제로 [Online game link](https://hexxagon.com/)에 접속하여 게임을 플레이해볼 수 있고, 모바일로도 여러 종류의 어플이 존재한다. 아래 사진은 위 링크에서 볼 수 있는 게임 진행 예시이다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Hexxagon/overview.gif?raw=true" width="600">
</center>

간략하게 게임 진행을 설명하면 다음과 같다.

- 플레이어는 각각 본인의 말과 가까운 위치(1칸 이내)에 본인의 말을 복사하거나,<br>본인의 말과 먼 위치(2칸)에 본인의 말을 이동할 수 있다.
- 말을 이동하거나 복사했을 때, 새로 생긴 말 주변의 상대의 말을 자신의 것으로 만든다.
- 게임을 진행하며 보드를 채워나가고, 다음의 경우에 게임이 종료된다.
  1. 보드가 꽉 차게 되었을 때.
  2. 한 플레이어의 말 수가 0개가 되었을 때
  3. 한 플레이어가 더 이상 움직일 수 없을 때.
- 게임이 종료되었을 때, 더 많은 말을 가지고 있는 사람이 승리한다.



나는 퍼즐게임을 좋아하고 나아가 과거에 Hexxagon을 굉장히 즐겨 했던 유저로써,  모바일로 이 게임을 할 수 있으면 좋겠다고 생각했으나, 현재 출시된 게임들은 두 가지 이유에서 만족스럽지 못했다.

1. AI의 수준.
   상대와 땅따먹기 형태의 대전을 하는 게임인 만큼 대전 AI의 수준은 매우 중요하다. 하지만 내가 해 본 게임들은 AI가 거의 없다시피 하였다.

2. 3인 모드, 맵 다양성 등의 한계.

   대부분의 Hexxagon 게임들은 1vs1vs1 모드를 지원하지 않고, 맵의 종류 또한 다양하지 않았다.

이런 아쉬움에 직접 원하는 기능이 포함된 Hexxagon 게임을 개발하기로 하였다.



## 기술 설명

### UI Components

게임에 사용되는 오브젝트들은 모두 일반 오브젝트가 아니라 UI Component이다. Unity에서 UI 객체들은 일반 객체들과 약간 다르게 관리된다. 사용자의 기기에 맞춰 특정한 위치에 객체를 고정시킬 수 있고, 다른 객체에 비해 높은 레이어에 렌더링된다. 또한, **클릭 이벤트를 간단하게 줄 수 있다.** 

타일을 클릭하며 진행되는 게임의 특성상, UI 객체로 제작하는 것이 유리하다고 생각하였고, 배경 파티클을 제외한 모든 요소를 UI Component로 만들게 되었다.



### 맵 제작

#### 개요

게임의 맵으로 정육각형 모양의 타일이 벌집처럼 배치하여 사용한다. 이를 1차원 배열로 제작할 것인지, 2차원 배열로 제작할 것인지를 고민하는 데에, 아래 두 개의 사항이 고려되었다.

1. 현재 타일을 기준으로 육각형 모양으로 범위를 인덱싱할 수 있어야 한다.
2. 다양한 맵의 제작으로 위해, 간단한 방법으로 각 타일을 제거할 수 있어야 한다.

육각형 형태의 타일이 지그재그로 배치되어 있기 때문에, 1차원 2차원 배열에 상관 없이 1번 문제를 해결하는 것은 난해하였다. 2번 문제를 고려할 때, 맵의 타일을 지우는 것에 있어 1차원 배열을 이용하는 것이 간단할 것이라고 생각했다. 

2차원 배열을 이용하면 맵 정보를 전달할 때 2차원 배열의 리스트인 3차원 배열을 전달해야 하지만, 1차원 배열일 경우 단순 숫자의 리스트인 1차원 배열을 전달하여 해결할 수 있기 때문이다.
{:.note}

따라서 1차원 배열로 맵 정보를 구성하였다.



아래 코드는 이와 같은 맵 정보를 코드로 나타낸 것이다.

```c#
public Gamemanager gamemanager;
private int[] index;
private int[] P1;
private int[] P2;
private int[] P3;
private string mapname;

public void map(int i)
    {
        switch (i)
        {
            /* 케이스 구성 설명
            
            case [int 맵번호] :
            	index = new int[] { [지울 타일 번호] };
            	P1 = new int[] { [플레이어1 시작 위치들] };
            	P2 = new int[] { [플레이어2 시작 위치들] };
                P3 = new int[] { [플레이어3 시작 위치들] }; // 3인 맵에서만 사용
            	
            	mapname = "맵 이름";
            	
            	// 2인, 3인에 따라 선택
            	gamemanager.mapsetting2(index, P1, P2, mapname);
            	gamemanager.mapsetting3(index, P1, P2, P3, mapname);
            */
            // 맵번호 0~39 : 2인용 맵
            // 맵번호 40~ : 3인용 맵
            case 0:
                index = new int[] { 0, 1, 7, 8, 9, 16, 17, 18, 26, 35, 53, 54, 62, 63, 70, 71, 72, 73, 79, 80 };
                P1 = new int[] { 2, 74, 44 };
                P2 = new int[] { 6, 36, 78 };
                mapname = "vainilla";
                gamemanager.mapsetting2(index, P1, P2, mapname);
                break;
            case 1:
                index = new int[] { 0, 1, 7, 8, 9, 16, 17, 18, 26, 35, 53, 54, 62, 63, 70, 71, 72, 73, 79, 80, 31, 39, 49 };
                P1 = new int[] { 2, 74, 44 };
                P2 = new int[] { 6, 36, 78 };
                mapname = "Three Bridge";
                gamemanager.mapsetting2(index, P1, P2, mapname);
                break;
                
			// 중략 //
           
            // 3인용 맵
            case 40:
                index = new int[] { 0, 1, 7, 8, 9, 16, 17, 18, 26, 35, 53, 54, 62, 63, 70, 71, 72, 73, 79, 80 };
                P1 = new int[] { 2, 78 };
                P2 = new int[] { 6, 74 };
                P3 = new int[] { 36, 44 };
                mapname = "vainilla";
                gamemanager.mapsetting3(index, P1, P2, P3, mapname);
                break;
            case 41:
                index = new int[] { 0, 1, 4, 5, 6, 7, 8, 9, 13, 14, 15, 16, 17, 18, 19, 23, 24, 25, 26, 27, 28, 35, 36, 37, 38, 45, 46, 53, 54, 55, 59, 60, 61, 62, 63, 67, 68, 69, 70, 71, 72, 73, 76, 77, 78, 79, 80 };
                P1 = new int[] { 2 };
                P2 = new int[] { 44 };
                P3 = new int[] { 74 };
                mapname = "Death match";
                gamemanager.mapsetting3(index, P1, P2, P3, mapname);
                break;

			// 테스트용 맵.
            case 999:
                index = null;
                P1 = new int[] { 36 };
                P2 = new int[] { 44 };
                mapname = "Debug";
                gamemanager.mapsetting2(index, P1, P2, mapname);
                break;
        }
    }
```



#### 인덱싱

맵은 총 81개의 타일로 구성된다. 왼쪽 아래에서부터 타일을 쌓기 시작한다. 이 때, 타일을 쌓기 시작하는 위치는 사용자의 휴대폰 화면의 중심을 기준으로 결정된다. 따라서, 어떤 기종의 휴대폰을 사용하더라도, 맵이 좌, 우로 치우치는 일 없이 중앙에 배치될 수 있다.

9개의 타일을 배치하고 나면, x좌표를 이동하고, y좌표에 유격을 주어 지그재그로 타일을 쌓는다.



#### 맵 제작 마무리

타일을 전부 배치한 후에는, 맵 번호에 맞게 불필요한 타일을 지우고, 일부 타일에 플레이어의 말을 배치하여야 한다. 맵 정보를 입력받아 2인, 3인 플레이 환경에 맞게 타일을 지우고, 플레이어의 말을 배치하여 맵 제작을 마무리한다.



아래 사진은 실제 맵이 만들어지는 과정으로, 게임상에서는 눈으로 구분할 수 없을 정도로 빠른 속도로 진행되나, 이해를 돕기 위해 딜레이를 주어 촬영하였다. <br>맵이 만들어지는 과정을 보면, 0번부터 80번까지의 81개의 타일이 순서대로 만들어진 후 필요 없는 타일이 삭제되고, 플레이어의 말이 배치되는 것을 볼 수 있다.
{:.note}

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Hexxagon/mapmaking.gif?raw=true" width="400">
</center>

 아래 사진은 실제 맵을 디자인할 때 구상하며 그렸던 그림이다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Hexxagon/mapdesign.png?raw=true" width="700">
</center>



### 게임 진행

#### 주변 타일 파악

게임 진행을 위해 자신의 말을 클릭하였을 때, 자신의 말 주변 1칸 거리에는 파란색을, 주변 2칸 거리에는 주황색 표시를 해 주어야 한다. 타일은 왼쪽 아래에서부터 순서대로 정렬되어 있으므로, 타일의 번호를 이용하여 주변 타일을 구할 수 있다. 

하지만, 타일이 지그재그로 배치되기 때문에, 모든 타일에 대해 같은 숫자를 넣어서 주변 타일을 구할 수는 없다. 아래 그림은 타일의 주변 타일 배치를 나타낸 것으로, 내가 누르는 타일이 홀수열인지, 짝수열인지에 따라 주변 타일의 숫자 배치가 달라지는 것을 볼 수 있다.

[이거 사진 프로필 지우고, 홀수열 짝수열 맞게 편집할 것]

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Hexxagon/tilenumber.png?raw=true" width="700">
</center>

최초 맵을 제작할 때, 한 열에 타일을 9개씩 배치하기 때문에, 타일 번호를 9로 나누어 홀수열, 짝수 열 여부를 구할 수 있으며, 이를 이용하여 주변 타일의 정보를 계산할 수 있었다.



##### 특수상황

위처럼 주변 타일 정보를 가져오면, 예외처리를 해줘야 하는 부분이 발생합니다. 아래 사진처럼, 18번 타일을 눌렀을 때, 멀리 있는 17번 타일에 파란 가이드가 활성화되게 되는 것이다. 이는 게임판 상에서는 서로 멀리 떨어져있기 때문에 활성화되지 않아야 하지만, 1차원 배열로 타일을 나열했기에 발생한 문제이다. 

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Hexxagon/spaceerror.gif?raw=true" width="500">
</center>



이를 해결하기 위하여 타일 사이의 거리를 활용하였다. 단순하게 현재 클릭한 타일과 일정 거리 이상이 되면 가이드를 활성화하지 않도록 처리하였다.

[원형으로 영역 잡아서 어디까지 활성화되는지 보이게 하는 그림 하나]



##### 최적화

초기 개발 버전에서, 주변 타일을 찾아올 때, 클릭할 때 마다 주변 타일을 계산하여 정보를 가져오도록 하였다. 하지만, 각 타일이 움직일 수 있는 위치는 고정적인데, 이를 매 클릭마다 가져오는 것은 비효율적이라고 생각하게 되었다.

따라서, 최초에 타일을 생성할 때, 각 타일에게 주변 타일 정보를 모두 가지고 있도록 코드를 변경하였다. 81개의 타일을 생성할 때 각 타일의 주변 타일 번호를 계산하고, 이 번호들을 각 타일에 저장한다.

이후 게임을 진행할 때는, 타일을 클릭했을 때 타일에 저장된 주변 타일 번호를 불러와서 요청을 보내어 간단하게 처리할 수 있었다.

[그림 하나 그리면 좋을 듯 하다]



### 게임 종료 조건 판단

이 게임은 총 세 가지 경우 게임이 종료되었다고 판단한다.

1. 모든 타일이 채워졌을 때
2. 한 플레이어의 말의 수가 0개가 되었을 때.
3. 한 플레이어가 더 이상 움직일 수 없을 때

이 중 1, 2번의 조건은 기존의 게임 진행 과정에 사용되는 변수로 간단하게 처리할 수 있었다. 하지만 3번의 경우 새로운 알고리즘을 구현해야 했다. 이를 구현하기 위하여, 빈 타일들을 이용하였다.

현재 턴이 n번 플레이어의 턴이라면, 모든 빈 타일을 순회하면서 n번 플레이어의 말이 빈 타일로 올 수 있는지 판단한다. 하나라도 n번 플레이어의 말이 이동할 수 있다고 판단되면, 바로 반복을 종료하며, 모든 타일을 순회하여도 움직일 수 있는 경우가 발견되지 않으면 n번 플레이어의 패배로 게임이 종료된다.

이 구현에는 두 가지 후보가 존재하였다. 첫번째 후보는 위에 설명한 것처럼 빈 타일을 순회하면서 움직일 수 있는 경우를 판단하는 것이고, 두번째 후보는 n번째 플레이어의 말을 전부 순회하며 움직일 수 있는 경우를 검색하는 것이었다. 두 알고리즘을 비교하여 첫 번째 방법을 선택하게 된 이유는 "두 알고리즘의 평균 반복 기댓값"이다. 게임 진행 초반에는, 대부분의 타일이 비어있으므로 1번 방법과 2번 방법 모두 조금의 반복으로 결론을 도출할 수 있다. 하지만 게임이 진행될수록, 2번 방법은 탐색해야 하는 횟수가 늘어나게 된다. 이를 고려하여 1번 방법을 선택하였다.



### AI

솔로 플레이를 위해 AI의 구현은 굉장히 중요한 과제이면서 동시에 어려운 과제였다. AI 구현은 게임을 설계했을 때 부터 시작했으며, 몇 주의 설계를 거쳐 간신히 원하는 형태로 구현할 수 있었다.
{:.note}



AI는 기본적으로 본인이 할 수 있는 모든 경우의 수를 계산한 뒤, 각각의 상황에 대해 가중치를 매겨 각 행동의 점수를 계산한다. 그 후 가장 높은 점수가 측정된 행동을 취한다. AI가 가중치를 부여하는 상황들은 아래와 같다.

1. 내가 움직이는 타일 주변 내 타일 수
2. 내가 움직이는 타일 주변 상대 타일 수
3. 내가 타일을 복사해 움직였을 때(파란 가이드로 이동했을 때)의 점수
4. 내가 타일을 이동해 움직였을 때(주황 가이드로 이동했을 때)의 점수
5. 내 타일이 도착할 곳 주변 상대 타일 수



게임의 구조를 간단하게 생각하여 상대의 말 하나를 얻으면 1점이 증가한다고 생각할 때, 5번 항목만 존재하면 AI가 동작할 수 있다. 하지만, 이렇게 했을 때는 원하는 수준의 AI를 만들 수 없었기에, 다양한 상황에 대한 고려를 하도록 AI를 개발하였다.

이를 위해 우선, 개발자가 직접 게임플레이를 하며 생각하는 과정을 순서도로 정리하였다. 이 과정에서 고려하는 상황들을 전부 체크하였고, 이 중 중요하다고 생각하는 것들을 추려서 AI가 고려하도록 만들었다. 위에 적은 1~5번 가중치에 대한 설명은 아래와 같다.



##### 1. 내가 움직이는 타일 주변 내 타일의 수

플레이어가 타일을 복사하지 않고 이동하게 되면, 지금 플레이어의 말이 있는 자리게 비게 된다. 그렇게 되면, 이 타일의 주변에 있는 나의 말들은 상대방이 이 타일을 차지함으로써 획득할 수 있는 점수가 된다. 따라서 이 가중치는 일반적으로 음수로 주어지게 되며, 내 말들의 방어를 위해 고려해야 하는 요소가 된다.



##### 2. 내가 움직이는 타일 주변 상대 타일의 수

1번과는 반대로, 플레이어가 타일을 비웠을 때, 다시 빈 타일을 차지함으로써 획들할 수 있는 점수가 된다. 또한, 상대방이 이 타일을 차지하도록 유도하여(상대방의 점수 방어를 위해) 상대방의 턴을 소비하게 할 수 있는 중요한 전략 요소이다. 이 값을 크게 할수록 상대방이 더 활발한 이동을 보이게 된다.



##### 3, 4. 내가 타일을 복사해 움직였을 때와 이동해 움직였을 때의 점수

3번은 단순히 내가 타일을 복사하였을 때 얻을 점수, 3번은 타일을 이동하였을 때 얻을 점수이다. 4번의 경우 주로 0보다 작거나 같은 값으로 주어지며, 3 - 4 의 값이 클수록, ai가 소극적인 움직임을 보인다.



##### 5. 내 타일이 도착할 곳 상대 타일 수

 가장 기본이 되는 점수로, 상대 타일을 하나 먹는 것을 얼마나 중요하게 생각할 것인가를 나타낸다. 이 변수값을 낮출 시, AI는 다양한 전략을 구사하지만 게임의 결과는 불리해지는 양상을 띄게 된다.



아래 영상은 적절한 수준의 ai와 대결 시연을 진행한 영상이다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Hexxagon/maintest.gif?raw=true" width="500">
</center>



#### 난이도 조절

AI가 각 상황에 부여하는 가중치를 변경함으로써 AI의 난이도를 설정할 수 있다.  아래 사진은, 동일한 상황에 대해 AI의 다른 반응을 테스트한 것으로, 가중치에 따라 AI의 행동양상이 크게 달라짐을 볼 수 있다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Hexxagon/aidif1.gif?raw=true" width="500">
	<img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Hexxagon/aidif2.gif?raw=true" width="500">

</center>

아래는 aiset에 대한 일부 코드이다.

```java
public void aiset(int i)
    {
        switch (i)
        {
            case 1:
                aindex = new int[] { -2, 1, 1, -3, 2 };
                break;
            case 2:
                // 노말 난이도
                aindex = new int[] { -4, 1, 1, -3, 2 }; 
                break;
                
            // 중략 //
            case 6:
                // 세미 하드
                aindex = new int[] { -1, 1, 2, 0, 3 }; 
                break;
                
            // 중략 //
                
            case 8:
                // 하드 (소극적)
                aindex = new int[] { -1, 3, 2, 0, 1 };               
                break;
        }

        this.gameObject.GetComponent<Gamemanager>().aidifficulty = aindex;
    }
```



## Assets

게임 개발에 필요한 그림 에셋은 전부 직접 그려서 사용하였다.<br>
아래 그림은 각 플레이어 말들의 등장 모션을 프레임 단위로 분리하여 나타낸 것이다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Hexxagon/PlayerMotion.png?raw=true" width="500">
</center>

에셋 제작을 위해 

## 한계

### 프로젝트 구조

이 프로젝트는 그 어떤 디자인 패턴도 사용하지 않았다. 개발 당시에 그런 지식을 하나도 보유하고 있지 않았고, 그저 개발이 편한 대로 개발하였다.

그 결과, gamemanager가 ai를 구동하기 위한 알고리즘을 포함하고 있는 등 기형의 코드가 탄생하였다. 1vs1에서 정상 작동하는 ai를 1vs1vs1에 적용하기 위해 ai를 별개의 파일로 분류해야 하지만, 이가 불가능할 정도로 코드가 섞이게 되었다. 결과적으로 1vs1vs1에는 ai를 적용하지 못하였다.



### 자료구조의 이해 

자료구조에 대한 이해가 부족하였다. 무르기 기능 등에 대해, 1차원 배열에 플레이어의 말과 상대의 말, 그 주변 정보까지 전부 담아놓았다. 이렇게 진행하였을 때, 개발자인 본인은 코드를 이해하는 것에 큰 어려움이 없지만, 이를 바탕으로 협업을 진행하게 된다고 가정했을 때, 다른 사람들은 코드의 이해에 오랜 시간을 할당해야 할 것이다.



## 추가

### 개발 과정

#### 주변 타일 탐색 기능 개발

주변 타일을 검색하는 기능을 구현하는 과정에서 촬영된 영상이다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Hexxagon/text_indexingerror.gif?raw=true" width="400">
</center>



#### 초기 플레이어 디자인

초기에 이 게임을 제작할 때는, 기하학적인 무늬를 적용한 말 디자인을 생각하였다. 이를 바탕으로 디자인을 진행해 게임에 적용하였으나, 생각했던 것보다 플레이어의 말이 통일감을 주지 못하고, 시선을 분산시켜 폐기하게 되었다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Hexxagon/protoversion.gif?raw=true" width="400">
</center>



#### 최초 테스트

밑의 영상은 앱을 실제로 빌드하여, 갤럭시 노트9에서 플레이한 것을 촬영한 것이다. 메인 페이지, 게임의 종료도 구현되지 않고 단순히 게임 진행만 가능한 상태로, 터치 오류 등을 찾기 위해 진행하였다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Hexxagon/gametest.gif?raw=true" width="400">
</center> 
