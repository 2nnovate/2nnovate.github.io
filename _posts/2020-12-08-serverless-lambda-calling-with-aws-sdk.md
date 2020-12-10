---
title: "nodejs 어플리케이션에서 aws-sdk 를 활용해 lambda 함수 호출하기"
layout: post
categories: [AWS, lambda, nodejs]
---

## summary

> lambda 에 http event 로 호출되는 함수를 작성 및 배포한다.
>
> nodejs 어플리케이션에서 aws-sdk 를 사용하여 해당 함수를 호출하고, 결과를 리턴받는다.

이해를 돕기위해 영문자를 모두 대문자로 변경하는 람다 함수를 만들어 업로드한 후,
nodejs 어플리케이션에서 해당 함수에 문자를 전달해 대문자로 된 문자를 리턴받아보는 예제를 만들어보자.

## 1. http 이벤트를 받는 lambda 함수 작성 및 배포

> http 이벤트로 동작하는 lambda 함수를 작성하고 배포한다.

*(이 포스팅에서는 [serverless project 생성]({{ site.baseurl }}{% link _posts/2020-12-03-serverless-and-lambda.md %})부분은 건너뛰도록 하겠다)*

### 1-1) serverless.yml 설정 변경

먼저 http 이벤트로 동작하도록 serverless.yml 파일을 수정한다.
*http 이벤트는 AWS 의 API Gateway 를 사용한다 (????)*

```yaml
functions:
  capitalize:
    handler: handler.capitalize
    events:
      - http:
            path: capitalize
            method: post
```
*./serverless.yml*

function 의 이름을 hello 에서 capitalize 로 바꾸었고, handler 파일에서 export 하는 함수의 이름도 capitalize 로 변경했다.

아래 event 부분에 주목하자.
* **- http:** <http 이벤트로 동작하는 설정을 하겠다는 의미>
    * **path:** <http 이벤트의 엔드포인트 path 를 설정하는 옵션>
    * **method:** <api 호출 메소드를 정의하는 옵션>

*자세한 options 에 대한 설명을 [링크](https://www.serverless.com/framework/docs/providers/aws/guide/serverless.yml/)를 참조하자.*

### 1-2) lambda 함수 작성

이제 handler 함수를 수정하도록 하자.

```javascript
module.exports.capitalize = async (event, context, callback) => {
  console.log('hello');
};
```
*./handler.js*

기존과 달라진 점은 함수 매개변수가 3개로 늘어났다는 점이다.
각각 parameter 를 간략히 설명하면 다음과 같다.
* event: 해당 람다 함수를 호출할 떄의 정보를 포함한다.
* context: 호출, 함수 및 설정에 대한 정보를 포함한다.
* callback: 응담을 전달하기 위한 함수로, 첫 번째 인자로는 Error, 두 번째 인자는 응답 객체를 사용한다.

*자세한 내용은 [AWS Lambda 함수 핸들러(Node.js)](https://docs.aws.amazon.com/ko_kr/lambda/latest/dg/nodejs-handler.html)를 참고하자.*

우리는 post method 로 body 를 통해 대문자로 변경할 문자를 전달받을 것이므로, 아래와 같이 설정한다.

```javascript
module.exports.capitalize = async (event, context, callback) => {
  const { targetText = '' } = event.body;
  if (!targetText) {
    callback(new Error('target text is required'));
    return;
  }
  
  if (typeof targetText !== 'string') {
    callback(new TypeError('target text is not string type'));
    return;
  }
  
  const response = {
    statusCode: 200,
    result: targetText.toUpperCase(),
  };
  callback(null, response);
};
```
*./handler.js*

`const { targetText = '' } = event.body;` 부분은 해당 람다 함수를 API Gateway 통해 호출했을 때 body 로 넘어오는 targetText 를 선언하는 부분이다.
tatgetText 가 없을 때와, string 형식이 아닐때 벨리데이션을 추가했다.
callback 함수는 첫 번째 인자로 Error 를 받으므로, 적절한 에러 객체를 전달하고 람다함수를 종료시킨다.
마지막 리턴부는 Error 인자는 null 로 비우고, 응담내용(Payload)를 response 객체로 만들어 전달한다.

mock 파일을 생성해 로컬 invoke 를 통해 해당 함수를 실행해보자.

```json
{
  "body": {
    "targetText": "2nnovate"
  }
}
```
*./eventMock.json*

```
sls invoke local -f capitalize --path ./eventMock.json
```

결과는 다음과 같다.

![capitalize example local invoke](/assets/images/capitalize-example-local-invoke.png)

### 1-3) lambda 함수 배포

이제 작성된 대문자 변환기를 AWS 에 배포해 보자.
serverless.yml 파일을 약간 손보도록 하자.

```yaml
service: capitalize-app
app: capitalize-app

provider:
  name: aws
  runtime: nodejs12.x
  region: ap-northeast-2
  profile: 2nnovate

functions:
  capitalize:
    handler: handler.capitalize
    name: capitalize-app
    events:
      - http:
          path: capitalize
          method: post
```
*./serverless.yml*

service, app, functions.capitalize.name 을 원하는 이름으로 설정한다. 배포되는 위치는 provider.profile 이 결정하는데,
이는 ./aws/credentials 에 설정된 profile 을 의미한다.

`sls deploy` 를 통해 배포하면 함수를 패키징하 S3 에 업로드하고 배포하는 과정을 serverless 프레임워크가 알아서 수행한다.

![capitalize app deploy](/assets/images/capitalize-app-deploy.png)

AWS 콘솔에 로그인 후 lambda 페이지로 이동하면 방금 배포한 capitalize-app 이 목록에 있는 것을 확인할 수 있다.

![deploy result in AWS lambda](/assets/images/AWS-lambda-list.png)

## 2. nodejs 앱에서 aws-sdk 를 통해 lambda 함수 호출

> lambda object 를 통해 람다 함수를 nodejs app에서 호출한다.

### 2-1) lambda invoker app 생성

~ 경로애서 capitalize-lambda-invoker 디렉토리를 생성하고, `npm init` 명령여를 통해 앱을 초기화 시키자.
초기화를 위한 질문을 설정하면*(모두 엔터를 치면 된다)* package.json 파일이 생성된다.

aws-sdk 를 `npm i aws-sdk` 을 통해 설치하도록 하자.
다음은 index.js 를 생성하고, input 문자를 전달받아 lambda 를 호출하는 앱을 작성해보자.

```javascript
const Http = require('http');
const Url = require('url');
const Querystring = require('querystring');

const aws = require('./aws');

const invoker = async (request, response) => {
  const { query } = request;
  const { targetText } = query ? Querystring.parse(query) : {};

  const params = {
    FunctionName: 'capitalize-app',
    Payload: JSON.stringify({
      body: {
        targetText,
      },
    }),
  };
  const lambda = new aws.Lambda({ apiVersion: '2015-03-31', region: 'ap-northeast-2' });
  const invokeResponse = await lambda.invoke(params).promise();
  const { result } = invokeResponse.Payload ? JSON.parse(invokeResponse.Payload) : {};

  response.writeHead(200);
  response.end(`target text: ${targetText || ''}\ncapitalize result: ${result || 'fail to capitalize'}`);
};

Http.createServer((request, response) => {
  const { url } = request;
  const { pathname, ...parsedUrlInfo } = url ? Url.parse(url) : {};
  if (pathname === '/') {
    return invoker({
      ...request,
      ...parsedUrlInfo,
    }, response);
  }

  response.writeHead(404);
  response.end(Http.STATUS_CODES[404]);

}).listen(4044);
```
*./index.js*

nodejs 에서 기본으로 제공하는 http, url, querystring 모듈을 이용하여 4444포트에 서버를 띄우고 path 가 '/' 인 요청에 대해서 lambda 함수를 호출한다.
query 로 전달받은 targetText 를 lambda 함수의 body 로 전달한다.
aws 설정을 ./aws.js 파일을 만들어 아래와 같이 한다.

```javascript
const AWS = require('aws-sdk');

const credentials = new AWS.SharedIniFileCredentials({ profile: '2nnovate' });
AWS.config.credentials = credentials;

module.exports = AWS;
``` 
*./aws.js*

SharedIniFileCredentials 를 이용하면 .aws/credentials 에 저장된 provider profile 을 이용할 수 있다.

```node index.js``` 를 실행한 후, 브라우저에서 `localhost:4044/?targetText=eloy` 와 같이 target text 를 설정해보자.
 
![invoke lambda result(success) by nodejs app](/assets/images/lambda-invoker-server-run.png)

만약 target text 를 전달하지 않으면 아래와 같은 메시지가 뜬다.

![invoke lambda result(fail) by nodejs app](/assets/images/lambda-invoker-fail-to-capitalize.png)

nodejs 앱은 query 를 통해 전달 받은 targetText 를 capitalize-app lambda 에 전달하고, 응답을 받아 브라우저에 표시한다.

## end

지난 포스트에서 알아본 lambda 를 nodejs 앱 환경에서 aws-sdk 를 통해 호출하고 응답을 받아오는 간단한 예제를 만들어 보았다.
다음 포스트에서는 좀 더 심화된 예제를 통해 헤드리스 브라우져에 대해서 알아보겠다.
