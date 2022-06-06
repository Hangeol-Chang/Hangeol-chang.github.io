---
layout: page
title: Lucid
description: >
  detail description about Lucid Project
---



0. this unordered seed list will be replaced by toc as unordered list
{:toc}

# About 



## 한계

### 프로젝트 구조

이 프로젝트는 각 객체별로 스크립트가 분리되어 있고, 이에 따라 동작하지만 그 경계가 매우 모호하게 설정되어있다.



### 지금 다시 개발한다면

- 스킬의 경우, 하나의 인터페이스를 만들어 모든 스킬에 상속시킨다.
  - 이렇게 제작할 시, 몬스터 등 스킬의 피격을 처리해야 할 때, 스킬 인터페이스를 넘겨받아 한번에 데이터를 처리할 수 있게 된다.
