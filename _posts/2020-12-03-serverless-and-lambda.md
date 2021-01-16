---
title: "serverless 프레임워크를 사용한 AWS lambda 배포 및 사용"
layout: post
categories: [AWS, lambda, Serverless]
---

## Summary

> FaaS 와 AWS Lambda 서비스를 이해한다.
>
> serverless framework 를 사용하여 람다 프로젝트를 생성하고, 로컬 환경에서 테스트 해 본다.
>
> serverless framework 로 Lambda 에 배포한다.

## 1. FaaS 와 AWS Lambda

> 서버를 관리 불필요, 지속적 크기 조정, 밀리초 단위로 측정, 일관된 성능

AWS lambda 서비스는 서버측의 코드를 서버를 띄우지 않고 실행시킬 수 있는 서비스이다.
기존 물리서버나, 클라우드 환경의 서버는 관리 및 유지보수에 수고가 든다는 단점이 있다.
lambda 서비스를 이용하면 서버리스 아키텍쳐를 구현할 수 있다.

서버의 아키택쳐는 아래의 순서로 발전(출시 - *이 전의 기술이 후의 기술보다 나쁘다는 의미가 아님*)되었다.

1. 물리서버
2. IaaS (Infrastructure as a Service)
3. PaaS (Platform as a Service)
4. 서버리스
    * BaaS (Backend as a Service)
    * FaaS (Function as a Service)
    
**물리서버**는 물리적인 서버이며, 전산실·서버실을 떠올리면 된다.

**IaaS (Infrastructure as a Service)** 는 물리적인 컴퓨터를 클라우드에서 제공하는 것을 의미하며,
AWS 의 ec2 등을 뜻한다.

**PaaS (Platform as a Service)** 는 IaaS 에서 한 번 더 추상화 된 개념으로, 네트워크와 런타임환경을 신경쓰지 않고
어플리케이션만 배포하면 되는 아키텍쳐를 의미한다. 대표적으로 AWS 의 beanstalk 가 있다.

**서버리스**는 BaaS 와 FaaS 가 있는데,
BaaS (Backend as a Service) 는 다양한 기능의 API 를 서비스로 제공해줌으로 클라이언트 개발에 집중할 수 있는 것을 말한다.
대표젹으로는 Firebase 가 있다.
FaaS (Function as a Service) 는 함수단위로 기능을 구현하여 이를 업로드 후 필요시마다 해당 함수를 호출하는 서비스를 의미한다.
이번 포스트에서 다루는 AWS 의 lambda 가 대표적이다.

lambda 는 따로 서버를 관리 할 필요가 없으며, 등록해 둔 함수가 호출 될 때 마다 비용을 측정해 청구된다.
즉, 함수단위로 업로드 한 서비스가 사용될 때마다 비용을 지불하면 된다.
또한 AWS 에서 최적의 상태를 자동으로 유지해주기 때문에 일관된 성능으로 동작가능하다.

*reference - [서버리스 아키텍쳐(Serverless)란?](https://velopert.com/3543)*

## 2. serverless framework 를 사용한 AWS lambda 서비스 사용

> zero-friction serverless development
>
> easily build apps that auto-scale on low cost, next-gen cloud infrastructure.

Serverless 프레임워크는 간편하게 서버리스 아키텍쳐를 빌드, 배포할 수 있는 도구이다.
AWS lambda 를 지원하며, azure 들 다른 서버리스 서비스들도 지원한다.

### 2-1) serverless 설치

serverless 를 설치 및 사용하기 위해 사전에 설치해야 하는 것들은 다음과 같다.
* nodejs (npm)
* AWS cli

사전에 설치가 필요한 것들이 모두 설치되었다면 npm 을 통해 serverless 를 전역 설치하도록 하자.

```
npm i serverless -g
```

### 2-2) serverless 프로젝트 생성

아래 명령어를 terminal 에 입력하면, 자동으로 몇 가지 설정을 물어보고 프로젝트를 initialize 해 준다.

```
serverless
```

주요 설정은 아래와 같다. 
* No project detected. Do you want to create a new one?
    * 새로운 프로젝트를 생성할지 여부를 묻는다. y 를 입력하고 엔터를 치면 다음 단계로 넘어간다.
* What do you want to make?
    * 서버리스 환경을 선택할 수 있다. AWS lambda 를 사용하고, 런타임 환경은 nodejs 를 사용하므로 **AWS Node.js** 를 선택하면 된다. 
* What do you want to call this project?
    * 프로젝트 명을 입력한다.

### 2-3) function 작성

생성된 serverless 디렉토리의 구조는 다음과 같다.

```markdown
serverless-test
    ㄴ .gitnore
    ㄴ handler.js
    ㄴ serverless.yml
```

serveless.yml 파일의 functions 부분을 보면 어떤 함수가 lambda 호출시 실행되는지 확인할 수 있다.

```yaml
functions:
  hello:
    handler: handler.hello
```
*serveless.yml*

hello 함수는 handler 에 정의되어 있는 hello 함수인 것을 알 수 있다.
테스트를 위해 해당 함수에 hello world 를 콘솔에 출력해보자.

```javascript
module.exports.hello = async (event) => {
  console.log('hello world');
};
```
*handler.js*

로컬에서 작성된 함수를 실행하는 방법은 다음과 같다.

```
sls invoke local —-function {함수명]
```

프로젝트 디렉토리에서 아래와 같이 입력하여 **hello world**가 잘 출력되는지 확인해보자.

```
sls invoke local —-function hello
```

*--function 옵션은 -f 로 축약해서 사용할 수 있다. (`sls invoke local —f hello`)*

![sls local invoke](/assets/images/sls-local-invoke-img.png)

위와 같이 console 에 출력한 hello world 가 제대로 찍힌것을 확인할 수 있다.

hello 함수를 잘 살펴보면, event 를 매개변수(parameter) 로 받고 있는데,
local invoke 시 mock 으로 event 를 만들어 전달할 수도 있다.

```json
{
  "test": "This is test mock"
}
```
*evnetMock.json*

```
sls invoke local —-function {함수명] --path {mock파일 경로}
```

로 mock 파일을 전달하여 invoke 할 수 있다.

```javascript
module.exports.hello = async (event) => {
  console.log('event');
};
```
*handler.js*

```
sls invoke local --function hello --path ./eventMock.json
```
![sls local invoke with mock file](/assets/images/sls-local-invoke-with-mock-file.png)

## 3. serverless framework 를 사용한 AWS lambda 배포

serveless 프레임워크를 사용하면 배포는 아래 명령어로 간단하게 할 수 있다.

```
sls deploy
```

만약 배포가 되지 않는다면 aws credential 이 설정되어 있지 않아서이니, [링크](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html)를 참조하여 설정하도록 하자.
만약 credential 에 설정된 계정에 권한이 부족할 수 도 있으니 [deploy 를 위한 최소 권한](https://github.com/serverless/serverless/issues/588)을 확인해보자.
*모든 권한을 주는 방법도 있다.*

## end

이번 포스팅을 통해 서버 아케텍쳐에 대한 간단한 이해와 severles 프레임워크를 통해 간단하게 AWS lambda 를 사용하는 방법에 개해서 알아보았다.
다음 포스팅에서는 nodejs 환경에서 aws-sdk 를 활용해 lambda 함수를 http 로 호출하는 방법에 대해서 알아보겠다.
