### About

안녕한걸의 개인 블로그 입니다.

**Hydejack** 테마를 사용하여 제작되었습니다.

이 밑에는 그냥 제가 이것저것 수정해 보면서 적어놓은 것들입니다.



## 시작

`cd documents/Github/Hangeol-chang.github.io`

`bundle install` 

`bundle exec jekyll serve`

 http://127.0.0.1:4000



# 구성

#### Portfolio

#### Life

- life
- it

#### Develop

- Solving
  백준, SWEA 등의 문제풀이 페이지.
- Studing

#### Learning

- 기계기사 등 개인적인 공부내용

#### SSAFY



### 글 작성

#### 제목 양식

```
2012-02-07-제목-띄어쓰기는-하이픈으로
```

#### 글 머리

```
---
layout: post
title: Example Content
description: >
  description 안써도 됨.
image: 
  path: /assets/img/blog/example-content-iii.jpg
  srcset:
    1060w: /assets/img/blog/example-content-iii.jpg
    530w:  /assets/img/blog/example-content-iii@0,5x.jpg
    265w:  /assets/img/blog/example-content-iii@0,25x.jpg
related_posts:
  - example/_posts/2017-11-23-example-content-ii.md
  - /example/2012-02-07-example-content/
sitemap: false
hide_last_modified: true
---
```

```
---
layout: post
title: Example Content
sitemap: false
hide_last_modified: true
---
```



이미지

<center>
<img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/경로/제목.png?raw=true" width="300">
</center>



#### 글에 쓰는 양식들

```markdown
0.
{:toc}

이렇게 쓰면 글이 회색이 됨.
{:.faded}

![img](https://이미지 주소)
{:.lead width="800" height="100"}
<!-- 이미지의 가로, 세로 지정 -->

큰 인용구
{:.lead}

*[HTML] : 위에 쓴 것에 설명을 달 수 있음.

<!-- 파일 연결 -->
[name]{:.heading.flip-title} 해 놓고,
[name]: 연결할 파일 명.md


```







## Learning

#### 알아낸 것들

- 서브폴더는 불가능함.
  하려면, 블로그를 hy 메인 깃허브로부터 완전 분리시켜야 됨.
  - but, 글 안에 링크로 이동시킬 순 있을듯.
- 글 제목에 한글이 있으면 안됨.



#### data

- 사이드바에 들어가는 것들이나, yml에서 객체형태로 작성하는 것들의 약어가 들어가 있음.



#### 메인 페이지?

기본적으로 최근 쓴 글들이 보이는데, 어케 바꿀 방법 없으까??



## Todo

- 공식문서 읽으면서, 필요없는 문서들 하나하나 지워가기.(거의 다 함)

- 태그 만들기.



- 백준 문제풀이 글들 옮겨오기
- 티스토리에 있는 글들 이식.



## Completed

- 이미지 전부 대체
