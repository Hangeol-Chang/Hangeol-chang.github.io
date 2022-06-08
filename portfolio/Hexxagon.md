---
layout: page
title: Hexxagon
description: >
  detail description about Hexxagon Project
---



0. this unordered seed list will be replaced by toc as unordered list
{:toc}

# About

이 페이지는 개인프로젝트로 진행하였던 게임 제작 프로젝트 **Hexxagon**에 대해 소개하고 있습니다. 게임 진행을 위한 로직들에 대한 설명과, 이를 구현 과정에서 발생하였던 중요 이슈 및 기타 사항들을 포함하고 있습니다.



## Abstract

Hexxagon은 1993년 개발된 보드게임으로, 크게 유명한 게임은 아니지만, 현재까지 형태를 유지해 오고 있다. 실제로 [Online game link](https://hexxagon.com/)에 접속하여 게임을 플레이해볼 수 있고, 모바일로도 여러 종류의 어플이 존재한다. 아래 사진은 위 링크에서 볼 수 있는 게임 진행 예시이다.



[게임 진행 gif 따서 넣을 것.]



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

기본 구성 내용~~

##### 특수상황

번호는 이어지는데 먼 것들 처리. [ <- 이거 유니티 c# 코드 수정해서 캡쳐 딸 것.]



### 게임 종료 조건 판단

이 게임은 총 세 가지 경우 게임이 종료되었다고 판단한다.

1. 

### AI

솔로 플레이를 위해 AI의 구현은 굉장히 중요한 과제였다.



### 최적화

#### 주변 타일 찾기

- 초기 개발 버전에서, 각 턴을 진행하는 데에 있어 주변 타일을 찾아올 때, 클릭할 때 마다 주변 타일을 계산하여 정보를 가져오도록 하였다.



## 한계

### 프로젝트 구조

이 프로젝트는 그 어떤 디자인 패턴도 사용하지 않았다. 개발 당시에 그런 지식을 하나도 보유하고 있지 않았고, 그저 개발이 편한 대로 개발하였다.

그 결과, gamemanager가 ai를 구동하기 위한 알고리즘을 포함하고 있는 등 기형의 코드가 탄생하였다. 1vs1에서 정상 작동하는 ai를 1vs1vs1에 적용하기 위해 ai를 별개의 파일로 분류해야 하지만, 이가 불가능할 정도로 코드가 섞이게 되었다. 결과적으로 1vs1vs1에는 ai를 적용하지 못하였다.



### 자료구조의 이해 

자료구조에 대한 이해가 부족하였다. 무르기 기능 등에 대해, 1차원 배열에 플레이어의 말과 상대의 말, 그 주변 정보까지 전부 담아놓았다. 이렇게 진행하였을 때, 개발자인 본인은 코드를 이해하는 것에 큰 어려움이 없지만, 이를 바탕으로 협업을 진행하게 된다고 가정했을 때, 다른 사람들은 코드의 이해에 오랜 시간을 할당해야 할 것이다.
