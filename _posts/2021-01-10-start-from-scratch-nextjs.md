---
title: "Next.js 를 이용한 SSR(Server Side Rendering)"
layout: post
categories: [React, Next.js, SSR]
---

## Summary

> SSR 에 대해서 알아본다.
>
> Next.js 를 통해 SSR 을 구현한다.

## SSR(Server Side Rendering)

> SSR은 서버에서 사용자에게 보여줄 페이지를 모두 구성하여 사용자에게 페이지를 보여주는 방식이다.

SSR 의 반대 개념은 CSR(Client Side Rendering) 이다.
두 방식은 어떤 차이점이 있을까?
먼저 CSR 이 브라우저에 페이지를 띄우는 방식부터 알아보자.

### CSR 방식일 때 브라우져가 페이지를 표시하는 방법

CSR 방식인 페이지에 처음 들어가면 브라우져는 아래의 순서를 거쳐 페이지를 표시한다.

1. 데이터가 없는 static 한 html 문서 다운로드
2. 자바스크립트 파일 다운로드
3. 자바스크립트 파일을 읽어 데이터 fetch가 필요할 경우 api 를 호출해 데이터 fetch
4. html 과 fetch 한 데이터를 합쳐 콘텐츠 표시

위의 네 단계를 거쳐서 페이지를 하기 때문에, 데이터 통신을 많이한다.
또한 맨 처음 html 문서를 다운로드 했을 때 내용이 들어가 있지 않기 때문에 SEO(검색엔진 최적화)에도 불리한 면이 있다.

### SSR 방식일 때 브라우져가 페이지를 표시하는 방법

반대로 SSR 방식은 한 단계로 모든 렌더링이 끝난다.

1. 데이터를 모두 포함한 html 문서 다운로드

html 문서 안에 모든 데이터가 포함되어 있기 때문에 SEO에도 유리하며, 페이지별로 추가적인 api 로딩 등이 필요하지 않다.

## Next.js

> Next.js는 인기 있는 경량의 프레임워크로 React로 만들어진 스태틱 서버 렌더링 애플리케이션입니다.

리액트 공식문서에서 Next.js 를 소개하는 문구이다.
Next.js 의 장점은 다음과 같다.

* 간단한 코드스플리팅
* 라우팅 및 pre-fetching 라우팅
* Zero config
* Built in CSS 지원
* 등등...

SSR 을 고려하고 있다면 Next.js 사용을 추천한다.

### 1. install

`npm i react react-dom next` 를 통해 세 가지 모듈을 한 번에 설치한다.
next 어플리케이션의 실행은 `next` 명령어를 통해 할 수 있다.
package.json 의 script 부분에 아래와 같이 스크립트를 추가하면 `npm run dev` 명령어로 next 앱을 실행시킬 수 있다.

```json
"scripts": {
  "dev": "next"
}
```

page 디렉토리를 만든 후, index.js 만들어 아래와 같이 작성해보자.

```javascript
const Index = () => (
  <div>
    <h1>
      Hello world
    </h1>
    <h2>
      Next.js
    </h2>
  </div>
);

export default Index;
```
*/pages/index.js*

특이한 점은, React 모듈을 따로 임포트 하지 않아도 된다는 점이다.
클래스형 컴포넌트를 사용하더라도 `extends React.Component` 와 같이 상속받아 사용하면 된다.

`npm run dev` 를 통해 앱을 실행시키면 localhost:3000 을 통해 해당 페이지를 볼 수 있다.
또한 Hot Module Replacement 이 자동으로 적용되어 있어,
파일을 수정하면 next 가 자동으로 다시 컴파일 해주므로 새로고침을 따로 할 필요가 없다.

### 2. 라우팅

페이지 라우팅을 할 때는 react-router 와 다른 점 두 가지만 유의하면 된다.

* to 가 아닌 href 로 라우팅 url 전달
* 라우팅 컴포넌트 하위에는 컴포넌트가 와야함

또한, Next.js 는 file-system based router 이기 때문에 파일명으로 라우팅을 할 수 있다.
pages 디렉토리 안에 about.js 파일이 존재하면, '/about' 페이지로 이동하면 pages/about.js 파일이 보여지게 된다.
*읽을 수 있는 확장자는 .js, .jsx, .ts, .tsx 파일이다.*

/pages 디렉토리에 about.js 파일을 생성하고 아래와 같이 작성해보자.

```javascript
const About = () => (
  <div>
    introduce...
  </div>
);

export default About;
```
*/pages/about.js*

그리고 index.js 파일을 수정해 about 페이지로 이동하는 링크를 만들어보자.

```javascript
import Link from 'next/link'

const Index = () => (
  <div>
    <h1>
      Hello world
    </h1>
    <h2>
      Next.js
    </h2>
    <Link href="/about">
      <a>About</a>
    </Link>
  </div>
);

export default Index;
```
*/pages/index.js*

#### 2-1) 쿼리 파라미터

쿼리 스트링은 props.url.query 객체를 통해 접근할 수 있다.
만얀 about.js 에서 name query 를 받는다면 아래와 컨포넌트 내부에서 props.url.query.name 을 통해 접근 가능하다.

```javascript
const About = (props) => (
  <div>
    Let me introduce my self, my name is {props.url.query.name}.
  </div>
);

export default About;
```
*/pages/about.js*

http://localhost:3000/about?name=eloy 로 접속하면, 아래와 같이 표시된다.

![nextjs routing query string](/assets/images/next-rounting-query.png)

#### 2-2) path 파라미터

`/blog/:id` 와 같이 id 파라미터를 사용하여 라우터를 구성하려면 어떻게 해야할까?
기본적으로 next 는 file-system based router 이기 때문에 id 에 해당하는 파일을 읽으려 할 것이다.
예를 들어 `/blog/2` 로 접근하면 `/pages/blog/2.js` 파일을 찾아 읽으려 할 것이다.

이를 해결하기 위해서는 next 의 Dynamic Routes 를 사용하면 된다.
pages 디렉토리 하위에 [id] 디렉토리를 생성하자.
그리고 [id] 디렉토리 하위에 index.js 파일을 생성하고 아래와 같이 작성하자.

```javascript
import { useRouter } from 'next/router';

const Blog = () => {
  const router = useRouter();
  const { id } = router.query;

  return (
    <div>
      <h1>Blog: {id}</h1>
    </div>
  )
};

export default Blog;
```
*/pages/blog/[id]/index.js*

[id] 디렉토리와 useRouter 를 통해 index.js 에서 path parameter 를 사용할 수 있다.
'pages/blog/2' 로 접속하면 실제로 `/pages/blog/2.js` 파일이 존재하지 않지만 post id 를 표시할 수 있다.

![nextjs routing path params](/assets/images/next-router-path-param.png)

### 데이터 fetching (pre render)

우리가 SSR 을 사용하는 이유이다.
즉, 서버에서 데이터를 포함하여 클라이언트에 뿌려주는 것인데, 이는 두 가지 방법으로 할 수 있다.

* getStaticProps
* getServerSideProps

두 방법 모두 사용법은 비슷하지만 html 파일이 생성되는 타이밍이 다르다.
getStaticProps 는 페이지를 빌들 할 때 한번만 생성하지만, getServerSideProps 는 요청이 있을 때 마다 생성한다.  
이런 차이점 때문에 Next.js 에서는 getStaticProps 방식을 권장한다.

#### getStaticProps

getStaticProps, getServerSideProps 두 방식 모두 page 에 각 함수명에 해당하는 async 함수를 export 함으로써 작동한다.
이 함수는 클라이언트 사이드에서 사용되지 않고, 서버에서만 사용된다.

테스트를 위해 제공되는 `https://jsonplaceholder.typicode.com/users` 를 사용하여 user 정보를 불러온 뒤 페이지를 렌더링 하는 예제를 만들어보자.
먼저 api 호출을 위해 axios 모듈을 설치한다.
설치는 `npm i axios`를 통해 진행한다.

pages 디렉토리 하위에 users.js 파일을 만들고, 아래와 같이 작성해보자.

```javascript
import axios from 'axios';

const Users = ({ users }) => {
  const userList = users.map(user => (
    <li key={user.id}>
      {user.username}
    </li>
  ));

  return (
    <ul>
      {userList}
    </ul>
  );
};

export async function getStaticProps() {
  const response = await axios.get('https://jsonplaceholder.typicode.com/users');
  const { data: users } = response;

  return {
    props: {
      users,
    },
  }
}

export default Users;
```
*/pages/users.js*

getStaticProps 를 통해 데이터를 불러온 뒤 리턴하면, Users 컴포넌트는 이를 props 로 받아서 사용할 수 있다.
`http://localhost:3000/users` 로 접속해서 개발자도구에서 network 탭을 보면,
`https://jsonplaceholder.typicode.com/users` 를 호출하는 부분을 보이지 않는다.
즉, 서버에서 data fetching 을 처리하고 data 를 포함한 html 페이지를 브라우져에 표시한 것이다.

![next pre fetching by getStaticProps result](/assets/images/next-pre-fetching-result.png)
![next pre fetching by getStaticProps browser network](/assets/images/next-pre-fetching-getStaticProps.png)

만약 Dynamic Routes 를 사용하는 컴포넌트라면 두 가지 함수를 export 하여 pre fetch 할 수 있다.
두 가지 함수는 getStaticPaths, getStaticProps 이다.

getStaticPaths 를 사용하여 어떤 path 를 pre-fetch 해야하는지 결정한다.
그리고 각 page 마다 pre fetch 하는 부분은 위와 같이 getStaticProps 를 통해한다.

```javascript
import { useRouter } from 'next/router';
import axios from "axios";

const Blog = ({ blog }) => {
  const router = useRouter();
  const { id } = router.query;

  return (
    <div>
      <h1>Blog: {id}</h1>
      <div>
        {blog.title}
      </div>
    </div>
  )
};

export async function getStaticPaths() {
  const blogIds = [1, 2, 3, 4, 5];
  const paths = blogIds.map(id => `/blog/${id}`);

  return { paths, fallback: false }
}

export async function getStaticProps({ params }) {
  const response = await axios.get(`https://jsonplaceholder.typicode.com/todos/${params.id}`);
  const { data: blog } = response;

  return {
    props: {
      blog,
    },
  }
}

export default Blog;
```
*/pages/blog/[id]/index.js*

위 예제에서는 getStaticPaths 에서 blogIds 를 상수로 정의헀지만,
여기서도 api 통신을 통해 blog 리스트를 불러온 뒤 getStaticProps 로 상세 내용을 불러올 수 있다.

#### getServerSideProps

getServerSideProps 도 async 함수를 export 하는것으로, getStaticProps 와 사용법은 동일하다.

```javascript
export async function getServerSideProps() {
  const response = await axios.get('https://jsonplaceholder.typicode.com/users');
  const { data: users } = response;

  return {
    props: {
      users,
    },
  }
}
```
*/pages/users.js*

자세한 data-fetching 에 대한 내용은 [공식문서](https://nextjs.org/docs/basic-features/data-fetching)를 참고하자.

## end

Next.js 를 통해 webpack 설정등을 건너뛰고 빠르게 SSR 페이지를 만들어 볼 수 있었다.
가볍고 빠른 SSR 클라이언트를 사용하길 원한다면 고려해보자.
이후 포스팅에서는 Next.js 로 만든 어플리케이션을 Serverless 프레임임워크를 통해 배포하는 방법에 대해 알아보겠다.
