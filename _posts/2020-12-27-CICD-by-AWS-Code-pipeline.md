---
title: "AWS CodePipeline, CodeBuild 를 통한 지속적 배포환경 구축"
layout: post
categories: [AWS, CodePipeline, CodeBuild, CI/CD]
---

## summary

> CI/CD 가 무엇인지 알아본다.
>
> AWS CodePipeline, CodeBuild 를 통해 지속적 배포 환경을 구축한다.

## CI/CD?

> CI: continuous integration
> CD: continuous delivery

CI/CD 를 한 마디로 정의한다면, '품질을 보장할 수 있는 코드베이스를 지속적으로 제공한다' 정도가 될 것 같다.

CI 는 지속적 통합으로, 공유 브랜치에 지속적으로 자신이 작업한 내용을 반영할 수 있는것을 의미한다.
이는 자동화된 테스트틀 통해 가능하게 할 수 있다.
만약 이런 장치가 없다면 한 개발자가 한 작업이 다른 개발자의 작업에 영향을 미쳐 서비스가 망가질 위험이 있을 수 있다.


CD 는 지속적 배포 의미한다.
CI 환경이 잘 구축되어 있다면, 운영환경에 최신 반영사항을 빠르게 배포하여 고객에게 최신 서비스를 지속적으로 제공할 수 있다.

*CI 환경이 제대로 구축되어 있지 않다면, CD 를 통해 지속적으로 배포하는 행위가 의미가 없을 수 있다.*

## AWS CodePipeline?

> 빠르고 안정적인 업데이트를 위한 지속적 전달 파이프라인 자동화.

![AWS CodePipeline](/assets/images/AWS-CodePipeline.png)

[AWS CodePipeline](https://aws.amazon.com/ko/codepipeline/)은 릴리즈 파이프라인을 구축할 수 있는 AWS 서비스이다.
코드의 변경 사항이 있을 때마다 사용자가 정의한 릴리스 모델을 기반으로 빌드, 테스트 및 배포 단계를 자동화한다.

CodePipeline 을 이용하면 github 의 특정 repository 가 변경된 것을 시작으로 릴리즈 파이프라인을 실행할 수 있다.
 
## CodePine 환경 구축

> github 의 특정 저장소의 코드가 변경된 것을 시작으로 하여 프로덕션환경에 변경사항을 배포한다.

[지속적 배포 파이프라인 설정](https://aws.amazon.com/ko/getting-started/hands-on/continuous-deployment-pipeline/)를 참고하여 작성하였다.

### 1. 배포 환경 생성

첫 번째 단계는 배포를 할 대상 환경을 설정하는 것이다.
이 포스팅에서는 AWS 의 Elastic Beanstalk 를 사용하여 서버 구성을 생략하여 진행한다.

[Beanstalk 콘솔](https://ap-northeast-2.console.aws.amazon.com/elasticbeanstalk/home?region=ap-northeast-2#/welcome)로 이동하자.
**'Create Application'** 을 클릭해 새 환경 구축을 시작하자.

어플리케이션 이름을 설정하고, 플랫폼을 선택한다.
본 포스팅에서는 nodejs 를 사용할 것이므로 nodejs 를 선택하고, 브랜치 및 버전을 선택한다.
설정을 완료하면 **'앱 생성'** 버튼을 통해 환경을 생성한다.

### 2. source 코드 가져오기 옵션 결정하기

배포할 코드 베이스를 가져오는 단계로, 아래 선택지 중 하나에서 코드를 가져올 수 있다.

* GitHub 레포지토리
* Amazon S3 버킷
* AWS CodeCommit 레포지토리

본 포스팅에서는 GitHub 저장소를 통해 코드를 가져오는 옵션을 통해 pipeline 을 구축할 것이다.
*배포를 원하는 저장소는 미리 생성되어 있어야 한다.*

### 3. pipeline 생성

[CodePipeline 콘솔](https://ap-northeast-2.console.aws.amazon.com/codesuite/codepipeline/pipelines?region=ap-northeast-2&pipelines-meta=eyJmIjp7InRleHQiOiIifSwicyI6eyJwcm9wZXJ0eSI6InVwZGF0ZWQiLCJkaXJlY3Rpb24iOi0xfSwibiI6MTAsImkiOjB9)에 접속하여 **'파이프라인 생성'** 버튼을 클릭하자.

#### 3-1) 파이프라인 설정 선택

![create new pipeline - pipeline config](/assets/images/create-new-pipeline-1.png)

파이프라인 이름과 서비스 역할을 새로 생성해 준다.
*기존에 가지고 있는 서비스 역할을 사용하려면 '기존 서비스 역할'을 설정하고, 아래 셀렉트 박스에서 선택해준다.*

#### 3-2) 소스 스테이지 추가

소스공급자에서 'Github(버전 2)'를 선택한다.
버전 2는 OAuth 앱을 사용하여 직접 GitHub 리포지토리에 액세스하는 버전 1 보다 권장되는 방식이다.
버전 2는 Github Apps 를 사용하여 인증을 관리하고 다른 리소스와 공유할 수 있는 장점이 있다.

##### AWS Connector 생성

연결 항목의 우측 버튼을 통해 새 연결을 생성하자.
연결에 사용할 이름을 설정하고 확인 버튼을 누르면 Github 으로 리다이렉션이 되고, AWS Connector 를 인증할 수 있는 화면이 나온다.

![create new AWS Connector for Github Apps](/assets/images/create-new-AWS-Connector.png)

'Authorize AWS Connector for Github' 버튼을 눌러 인증을 완료하자.
인증 완료 후 새 Github 앱 설치를 통해 연결할 계정(혹은 TEAM)을 선택하자.
모든 저장소에 권한을 주거아 권한을 줄 저장소를 선택할 수 있다.
본 포스팅에서는 모든 저장소에 권한을 주는 Github 앱을 생성할 것이기 때문에 'app repositories' 옵션을 선택하고 진행한다.

![link to github](/assets/images/link-to-github.png)

생성이 완료되면 자동으로 github app 이 선택된다.
연결 버튼을 눌러 source 연결을 구성한다.

![create new pipeline - source stage](/assets/images/create-new-pipeline-2.png)

불러올 레포지토리와 브랜치를 설정한 후 '다음' 버튼을 눌러 '빌드 스테이지' 환경 설정으로 넘어가자.

#### 3-3) 빌드 스테이지 추가

빌드 스테이지 추가 단계에서는 코드를 테스트하고 컴파일하는 단계를 추가할 수 있다.
아래 두 가지 옵션을 선택할 수 있으며, 사용하지 않는 경우 건너뛸 수 도 있다.

* AWS CodeBuild
* Jenkins

![create new pipeline - source stage](/assets/images/create-new-pipeline-3.png)

본 포스팅에서는 AWS CodeBuild 를 사용하여 빌드스테이지를 구성하도록 하겠다.

##### AWS CodeBuild 생성

**'프로젝트 생성'** 버튼을 눌러 새 빌드 프로젝트를 생성하자.

빌드 프로젝트 환경설정을 원하는 대로 구성한 후, **'CodePipeline으로 계속'** 버튼을 눌러 파이프라인 설정을 계속 하자.
*CodeBuild 를 사용하면, buildspec.yml 파일 설정을 통해 빌드시 필요한 명령어를 실행할 수 있다. 자세한 내용은 [공식문서](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html)를 참조하자.*

필자는 환경 및 BuildSpec 항목을 아래 그림과 같이 설정하였다.

![create new code build project](/assets/images/create-codebuild-project.png)

새로운 CodeBuild 프로젝트가 생성되면 다음과 같이 CodePipeline 설정 화면에서 생성된 CodeBuild 프로젝트가 선택된다.

![create new pipeline - source stage done](/assets/images/create-new-pipeline-4.png)

#### 3-4) 배포 스테이지 추가

공급자를 Elastic Beanstalk 로 선택하고, 1단계에서 생성한 어플리케이션과 환경을 선택한다.

![create new pipeline - source stage done](/assets/images/create-new-pipeline-5.png)

#### 3-5) 검토 및 완료

검토 단계에서 모든 파이프라인 설정을 검토한 후, 생성버튼을 눌러 파이프라인을 생성하자.
생성이 완료되면 자동으로 파이프라인이 실행되며, 각 단계에서의 세부정보를 확인 할 수 있다.

![CodePipeline progress](/assets/images/code-pipeline-progress.png)

세부 정보를 눌러 확인해보면, codebuild 단계에서 buildspec.yml 설정파일이 없어서 실패한 것을 확인할 수 있다.
해당 파일을 생성하고 설정 master 에 commit 하면 자동으로 pipeline 이 실행되고, 정상적으로 완료되는 것을 확인할 수 있다.

![CodePipeline progress succes](/assets/images/code-pipeline-success.png)

## end

AWS 의 CodePipeline, CodeBuild 를 사용하여 배포환경을 구축하였다.
테스트의 자동화 및 디테일한 설정은 TEAM 혹은 개인이 Rule 을 통해 구성하도록 하자.








