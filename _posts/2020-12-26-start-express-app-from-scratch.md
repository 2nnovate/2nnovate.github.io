---
title: "Express app 시작하기"
layout: post
categories: [nodejs, express]
---

## Express

> Fast, unopinionated, minimalist web framework for Node.js

[Express js](https://expressjs.com/) 는 nodejs 웹 프레임워크로, 웹서버를 빠르고 쉡게 구현할 수 있도록 도와준다.
이번 포스팅에서 Express 프로젝트를 생성하고, hello world 를 뛰워본 후, github 에 올려 형상관리 하는 방법에 대해 알아보도록 한다.

## create github repository

> 깃헙 저장소를 통해 프로젝트의 형상을 관리한다.

생성할 프로젝트의 버전 및 형상 관리를 위해 github repository 를 생성한다.
형상관리가 필요없다면 이 단계를 건너 뛰어도 좋다.

[github](https://github.com/)에 접속해 로그인 후, repositories 페이지로 이동한다.
**new** 버튼을 통해 새로운 저장소를 만든다.

![create new repository on github](/assets/images/create-new-repository.png)
저장소명을 설정하고, 설명 입력 및 README 파일을 생성할 지 여부를 선택하고 **Create repository**버튼을 눌러 저장소를 생성하자.

![clone by https](/assets/images/clone-repository-by-https.png)
저장소를 생성한 뒤, Code 버튼을 통해 해당 저장소를 local 환경에 복제하자.
복사된 주소 앞에 `git clone (복사된 https 주소)` 불여 아래와 같이 실행한다.
*project 폴더가 생성되면 clone 되기 때문에 폴더를 생성하고자 하는 디렉토리에서 실행하도록 한다.*

![git clone](/assets/images/git-clone.png)

clone 된 디렉토리로 이동하면 Express 어플리케이션을 시작할 준비가 완료된다.

## Express 설치

> Express 프레임워크를 npm 을 통해 설치한다.

먼저 프로젝트를 initialize 하도록 하자.
초기화를 통해 package.json 파일을 생성한다.
`npm init` 명령어를 입력하면 cli 를 통해 간단한 config 를 한 뒤 초기화가 진행된다.
특별히 설정할 내용이 없다면 enter 를 치면서 넘어가면 된다.

package.json 파일이 생성되었다면, 아래 명령어를 통해 Express 프레임워크를 설치하자.

```
npm install express --save
```

*node_modules 디렉토리 하위의 파일들이 diff 에 잡힌다면, .gitigore 에 node_modules를 추가하자.*

## Hello World

> 3000 포트를 리스닝하고, / 에 접속하면 'Hello World' 문구를 보여주는 app 을 작성해보자.

루트 디렉토리에 app.js 파일을 생성하고, 아래와 같은 코드를 작성해 보자.

```javascript
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`);
});
```
*app.js*

express 모듈을 임포트 하고, listen 메소드를 통해 3000번 포트를 리스닝 하도록 했다.
콜백함수에 제대로 리스닝하고 있다는 것을 표시하기 위해, 콘솔을 찍었다.

app.get 메소드를 통해 '/'에 접속했을 때 'Hello World'를 표시하도록 하였다.
app.get 의 get은 http 메소드를 의미하며, get 이외에도 post, delete, put 를 지원한다.
자세한 내용은 공식 문서의 [기본 라우팅](https://expressjs.com/ko/starter/basic-routing.html)을 참고하도록 하자.

콜백함수의 req, res 는 각각 요청 객체와 응답 객체를 의미하며, res.send 를 이용하면 응답을 클라이언트로 바로 보낼 수 있다.
요청 객체와 응답 객체의 메소드는 [공식 API 문서](https://expressjs.com/ko/4x/api.html)를 참조하도록 하자.

앱을 실행시키는 방법은 다음과 같다.
터미널에서 `node app.js` 를 통해 node 앱을 실행시킨다.
*nodemon 을 사용하는 경우 `nodemon app.js`로 실행시키면 변경사항이 있을 때 마다 앱을 자동으로 재실행 해준다.*

## end

Express 프레임워크를 이용하여 간단히 서버 어플리케이션을 만들어 보았다.
http 의 다양한 메소드를 사용하여 자신만의 앱을 만들어 보자.
