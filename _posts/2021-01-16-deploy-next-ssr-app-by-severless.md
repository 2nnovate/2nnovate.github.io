---
title: "Serverless-next.js 를 사용한 Next.js SSR 어플리케이션 배포"
layout: post
categories: [Next.js, SSR, Serverless]
---

## summary

> Serverless-next.js 가 사용하는 AWS 서비스에 대해 간략히 알아본다.
>
> Next.js SSR 프로젝트를 Serverless-next.js 를 사용하여 배포한다.

## Serverless-next.js?

> Deploy serverless next apps with ease

[Serverless-next.js](https://www.serverless.com/plugins/serverless-nextjs-plugin/)는 Next.js 프로젝트를 쉽게 배포할 수 있게 해주는 Serverless 플러그인이다.
Serverless-next.js 가 사용하는 아키텍쳐는 아래 이이미와 같은 모습이다.

![Serverless-next.js architecture](/assets/images/serverless_nextjs_lambda_edge_aws_architecture.png)

간단히 설명하자면 Serverless-next.js 가 하는 역할은 이렇다.
AWS CloudFormation을 통해서 Next.js 프로젝트를 S3 버킷을 만들어서 업로드 한 뒤,
그 버킷에 올라간 프로젝트를 가져다 응답해줄 Lambda@Edge를 포한한 CloudFront 구성한다.

즉, Serverless-next.js 가 사용하는 AWS 서비스는 아래와 같다.
* CloudFormation
* S3
* CloudFront
* Lambda@Edge

이제 각각 서비스에 대해서 간략하게 알아보자.

### AWS CloudFormation

> Infrastructure as Code로 클라우드 프로비저닝 가속화

CloudFormation 은 인프라를 코드(yml/JSON)로 관리하는 것을 의미한다.
이렇게 코드로 인프라스트럭쳐를 관리하면 형상을 관리할 수도, 복사하기도, 지우기도 편하다는 이점이 있다.

자세한 내용은 [AWS CloudFormation](https://aws.amazon.com/ko/cloudformation/)를 참고하자.

### AWS S3

> 어디서나 원하는 양의 데이터를 저장하고 검색할 수 있도록 구축된 객체 스토리지

S3(Simple Storage Service) 는 객체 스토리지 서비스로, 파일을 업로드, 다운로드 할 수 있는 저장소라고 생각하면 편하다.
세부적 엑세스르 정교하게 설계하고 관리할 수 있는 이점이 있다.

자세한 내용은 [AWS S3](https://aws.amazon.com/ko/s3/)를 참고하자.

### AWS CloudFront

> 빠르고 고도로 안전하며 프로그래밍 가능한 콘텐츠 전송 네트워크(CDN)

CloudFront 는 AWS 에서 제공하는 CDN(고속 콘텐츠 전송 네트워크) 서비스로, 서버와 사용자 사이의 물리적 거리를 줄여 콘텐츠 로딩 속도를 최소화해주는 서비스이다.
전세계에 Edge server 를 두고, 사용자가 요청하는 데이터를 Cache 하여 데이터의 로딩을 최소화 한다.
Edge server 는 리전보다 훨씬 수도 많고 다양한 위치에 있으므로 사용자에게 더 빠르데 데이터를 제공할 수 있다.

클라이언트가 엣지서버에 리소스를 요청하면, 가장 가까이 있는 엣지서버에 캐싱되어 있는 리소스를 리턴한다.
이 때 캐싱되어 있던 데이터가 없으면 오리진에 요청하여 받아 온 후 받은 데이터를 캐싱한다.

자세한 내용은 [AWS CloudFront](https://aws.amazon.com/ko/cloudfront/)를 참고하자.

### AWS Lambda@Edge

> 사용자에게 더 가까운 위치에서 코드 실행

Lambda@Edge 는 클라우드프론트의 기능 중 하나로, 엣지서버에서 실행되는 람다함수를 의미한다.
일반 람다는 리전에서 실행되는 것에 비해서 Lambda@Edge 는 엣지서버에서 실행되므로 더 사용자에게 제공되는 이점이 있다.

자세한 내용은 [AWS Lambda@Edge](https://aws.amazon.com/ko/lambda/edge/)를 참고하자.

## Serverless-next.js 를 사용해 Next.js 프로젝트 배포

Serverless-next.js 가 사용하는 AWS 서비스를 간략히 알아보았으니 실제로 배포를 해보자.
Next.js 프로젝트에 아래 모듈을 dev dependency 로 설치하자.
* serveless
* serverless-nextjs-plugin

`npm i --save serveless serverless-nextjs-plugin` 명령어를 통해 설치하자.
모듈을 설치했으면, 프로젝트의 루트 디렉토리에 serverless.yml 파일을 생성하자.

```yaml
프로젝트이름:
  component: "@sls-next/serverless-component"
  inputs:
    bucketName: 사용할 S3 버킷 이름
``` 
*./serverless.yml*

프로젝트 이름을 설정하고 component 를 `@sls-next/serverless-component`로 설정하자.
inputs 를 통해 다양한 설정을 할 수 있는데, 여기서는 버킷의 이름만 설정했다.
option 을 customize 하는 것은 [공식문서](https://www.serverless.com/plugins/serverless-nextjs-plugin/)를 참고하자.

설정을 완료한 뒤, 터미널에서 `serveless` 를 실행하면 Serverless-next.js 가 CloudFormation 설정을 통해 S3, CloudFront, Lambda@Edge 를 자동으로 구성하여 배포한다.

![Serverless-next.js deploy result](/assets/images/serverless-nextjs-deploy-result)

결과에 있는 appUrl 로 접속하면 배포 결과를 확인 할 수 있다.
물론, domain 설정도 가능하다. [공식문서](https://www.serverless.com/plugins/serverless-nextjs-plugin/)를 참고하자.
*Lambda@Edge 는 us-east-1 에 배포된 것처럼 보이지 CloudFront 를 통해 모든 리전, 엣지에 배포된것이므로 걱정하지 않아도 된다.*

## end

Serverless-next.js 를 사용하여 Next.js SSR 어플리케이션 배포하는 과정에 대해서 알아보았다.
필요한 설정을 커스터마이징 하여 손쉽게 Next.js 앱을 배포해보자.
