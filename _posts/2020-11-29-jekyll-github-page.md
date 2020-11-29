---
title: "github page + jekyll 으로 정적 블로그 페이지 만들기"
layout: post
---

## summary

> github pages 를 이용해 정적 페이지를 호스팅한다.
>
> jekyll 을 통해 markdown 으로 정적 페이지를 쉽게 생성한다.
>
> jekyll theme 를 이용해 손쉽고 빠르게 페이지를 구성한다.

## github pages

> github 에서 제공하는 무료 호스팅 서비스.

웹 페이지를 모든 사람들이 접속할 수 있도록 하려면 일련의 과정들을 거쳐야 한다.
만든 페이지를 웹 서버에 올리고 도메인을 구매하고 연결 하는 등의 작업이 필요하다.
하지만 이 과정들을 직접하기에는 꽤 않은 시간과 노력을 들여야한다.
이 과정들을 간단하게 제공하는 서비스가 있는데, 개발자들에게는 익숙한 github 에서 제공하는 pages 서비스이다.

page 는 repository 이름을 자신의 user name 을 활용하여 만들면 활성화돤다.
새 저장소를 만들고, 저장소명을 `[username].github.io` 와 같이 만들면 자동으로 github pages 가 활성화 되어, 해당 url 으로 접속하면 자신의 페이지를 볼 수 있다.

예를 들어 github user name 이 eloy 라면 아래와 같이 repository 생성을 히면 된다.

`eloy.github.io`

저장소를 생성 한 뒤, 해당 저장소를 clone 한 뒤, index.html 파일을 생성해보자.

생성한 뒤, origin 에 해당 파일을 push 하면 repository 이름인 url 로 접속하면 index.html 의 내용을 확인할 수 있다.

[github pages](https://pages.github.com/) 링크로 들어가면 step by step 으로 github pages 를 생성하는 방법을 설명해주니 참고하면 좋겠다.

## jekyll

> 정적 페이지 생성기로, markdown 으로 작성된 문서를 미리 정의된 layout 을 통해 페이지를 생성해준다.

웹 페이지는 여러개의 페이지를 통해 이루어진다. 간단한 블로그를 예를 들어도 여러 개의 페이지가 필요하다.
간단한 블로그 페이지를 생각해도 필요한 페이지들은 다음과 같다.

- Home 페이지
- 소개 페이지
- 게시글 페이지

이를 각각 html 파일로 생성한다면 아래와 같이 되겠다.

- index.html
- about.html
- post1.html
- post2.html

게시글 같은 경우는 작성할 때 마다 html 페이지가 늘어나기 때문에 매번 html 페이지를 편집, 작성하기 번거로워지기 마련이다.
또한, 게시글은 모두 같은 layout 을 공유할텐데 같은 html, css 코드가 반복되는 문제도 있을 것이다.

jekyll layout 과 content 를 분리하여 관리해서 위와 같은 문제를 해결하고, 정적 페이지를 한 번에 생성해 준다.

jekyll 을 시작하는 방식은 [링크](https://jekyllrb-ko.github.io/docs/)를 참고하자.

[jekyll 단계별 튜토리얼](https://jekyllrb-ko.github.io/docs/step-by-step/01-setup/)을 통해 jekyll 의 동작 원리와 활용법을 익힐 수 있다.

## jekyll theme 적용하기

> 다른 사람들이 만들어 놓은 theme 를 내 블로그 페이지에 바로 적용한다.

jekyll 은 다른 사람들이 만들어놓은 theme 를 쉽고 빠르게 적용할 수 있는 장점이 있다.

일단 마음에 드는 theme 를 골라보자

- [https://jekyllthemes.io/free](https://jekyllthemes.io/free)
- [http://jekyllthemes.org/](http://jekyllthemes.org/)

위의 링크를 통해 마음에 드는 테마를 선택했다면, 해당 테마의 github repository 로 이동한다.
그 뒤, 해당 repository 를 fork 한다. 

fork 는 해당 repository 의 내용을 자신의 저장소로 복사해 오는 것을 의미한다.
우측 상단의 fork 버튼을 누르면 손쉽게 해당 repository 를 복사할 수 있다.

fork 가 완료되면 자동으로 나의 복사된 repository 로 이동되는데, 여기서 상단 메뉴중 `settings` 로 이동한다.

`settings -> options -> repository name` 을 통해 repository 의 이름을 변경할 수 있는데, 이 때 github pages 를 사용할 수 있는 조건으로 이름을 변경하기만 하면 된다.

예를 들어 github user name 이 eloy 라면 아래와 같이 repository 이름을 변경히면 된다.

`eloy.github.io`

이렇게 하면 간단히 jekyll theme 를 내 page 에 적용할 수 있다.

원하는 theme 를 적용한 뒤, git clone 을 통해 로컬에 환경을 구축하고 `_config.yml` 을 수정하자.

`jekyll serve` 를 터미널에 입력하여 로컬에서 수정한 페이지를 바로바로 확인 할 수 있다.

## end

jekyll 활용법을 숙지하여 원하는 테마 위에서 content 를 앞으로 잘 쌓길 바란다.
(참고로 이 블로그에 적용된 테마는 [Contrast theme](https://github.com/niklasbuschmann/contrast) 이다.)
