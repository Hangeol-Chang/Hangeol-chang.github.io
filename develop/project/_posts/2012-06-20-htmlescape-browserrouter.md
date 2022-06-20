---
layout: post
title: .[HTML 방탈출] React BrowserRouter, react-router-dom 배포 404 에러
hide_last_modified: true
---

>   github pages react-router-dom 배포시 발생하는 404 에러



0. 목차
{:toc}

## 개요

html방탈출에 한동안 손을 못 대다가, 이제 다시 여유가 생긴 것 같아 손을 좀 보고 있다. 

초기 버전을 깃허브에 deploy하는 과정에서 문제가 좀 생겼는데, 구글링을 해도 해결책이 잘 나오지 않고, 내가 찾은 해결책도 예전에 쓰인 글이라 지금과는 조금 달랐다.

그래서 나와 같은 사람들에게 좀 도움이 되라고 글을 남긴다.



이 블로그까지 찾아왔으면 도대체 얼마나 많은 자료를 찾아봤을지 상상이 안된다.ㅠㅠ 해결이 되었으면 좋겠다.
{:.note}



## 무슨 문제?

내가 작성한 React 프로젝트는 BrowserRouter를 사용하였다.

깃허브 페이지를 이용하여 서비스할 계획이었기 때문에, package.json에 아래처럼 hompage를 작성하였고,

```json
"homepage": "https://hangeol-chang.github.io/htmlescape",
```

라우팅 코드는 아래처럼 작성하였다.

```react
import {BrowserRouter as Route, Routes} from 'react-router-dom'

{/* 중략 */}

<BrowserRouter basename={process.env.PUBLIC_URL}>
	<Routes>
    	<Route exact path="/" element={<Intro />} />
        <Route exact path="/stage1" element={<Stage1 />} />
    </Routes>
</BrowserRouter>
```



이 코드가 분명 local에서는 제대로 동작을 한다. url에 직접 입력하여 접속하여도 접속이 가능하고 Link 태그를 이용해서도 정상적인 접근이 가능하지만, **깃허브를 통해 배포하면 작동하지 않는다.** 아예 BrowserRouter 안에 있는 요소들이 렌더링조차 되지 않는다.



## 해결책

문제의 원인은 생각보다 단순했다. 그냥 깃허브(github.io)에서 BrowserRouter를 지원하지 않는다........

더 정확히는 HTML5 pushState history API를 지원하지 않는다고 한다. 따라서, 울며 겨자먹기로 HashRouter를 사용해야 한다. 사실 뭐가 더 좋은건지 잘 모르겠다.

바꾼 코드는 아래와 같다.



```react
import { HashRouter as Router, Route, Routes} from "react-router-dom";

{/* 중략 */}

<Router>
    <Routes>
        <Route exact path="/" element={<Intro />} />
        <Route exact path="/stage1" element={<Stage1 />} />
        <Route exact path="/stage2" element={<Stage2 />} />

        <Route exact path="/clear" element={<Clear />} />
    </Routes>   
</Router> 
```



### 추가

바꾸고 달라진 점이 하나 있는데, URL로 접근이 불가능해졌다. 좋은건가?



##### 참고자료

공식문서 : [create-react-app.dev/docs/deployment/#notes-on-client-side-routing](https://create-react-app.dev/docs/deployment/#notes-on-client-side-routing)

