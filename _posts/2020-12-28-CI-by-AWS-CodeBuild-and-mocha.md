---
title: "AWS CodeBuild, Mocha, Chai 를 CI 환경 구축"
layout: post
categories: [AWS, CodeBuild, Mocha, Chai, CI/CD]
---

## summary

> 테스트 코드의 중요성을 알아본다.
>
> Mocha, Chai 를 통해 케이스 별로 단위테스트 하는 방법에 대해 알아본다.
>
> AWS CodeBuild 에서 build 할 때 마다 테스트를 하도록 테스트를 자동화 하여 CI 환경을 구축한다.

## 테스트 코드

> 저희는 테스트 코드 안 짜요.

화가 나는 말이다.
이런 소리를 하는 사람들이 하는 변명은 대개 아래와 같을 것이다.
* '시간만 더 걸릴 뿐이다.'
* 'QA 단계에서 버그를 잡으면 된다.'
* '의미 없다.'

이런 말을 하며 테스트 코드를 폄하하는 사람들은 [Expert Beginner](https://medium.com/@jwyeom63/%EB%8D%94-%EC%9D%B4%EC%83%81-%EB%B0%B0%EC%9A%B0%EB%A0%A4-%ED%95%98%EC%A7%80-%EC%95%8A%EB%8A%94-%EA%B0%9C%EB%B0%9C%EC%9E%90-expert-beginner%EC%9D%98-%EB%93%B1%EC%9E%A5-dd40c40aeedf)일 가능성이 높다.
*(재미있는 글이다, 한 번 읽어보길 추천한다)*

위와 같은 변명을 하는 사람이 주변에 존재한다면(없길 바란다), 아래와 같이 대답해줘라.
* 테스트 코드를 짜는데 시간이 더 걸린다는 말을 하며 짠 코드가 여기저기에서 문제를 발생시킨다면, 해당 코드를 수정하는데 걸리는 시간이 훨씬 많을 것이다.
* 함수 하나, 클래스를 하나 짤 때마다 QA 팀에게 부탁할 것인가? QA 팀이 있는 규모의 회사가 아니라면 어떻게 할 것인가?
* 제대로 해보고서 의미없다고 하는건지 정말 궁금하다.

**정말로 성장하고 싶다면, 진정으로 효율성 있는 코드를 짜고 싶다면 테스트 코드를 작성하라.**

## Mocha, Chai 를 사용한 단위 테스트

> 테스트에는 여러가지 방법이 있지만, 그 중 단위 테스트를 Mocha, Chai 를 통해 구현해보자.

Mocha 는 테스트 러너를 지원하는 테스트 프레임워크이며, Chai 는 Assertion 라이브러이이다.

Mocha 를 사용해 테스트를 정의하고 실행하며, Chai 를 통한 선언이 테스트 케이스를 통과하는지 여부를 검사한다고 생각하면 좋겠다.

### Mocha, Chai 설치 및 실행

`npm i mocha --save` 명령어를 통해 dev-dependency 로 mocha 를 설치한다.
같은 방식으로 `npm i chai --save` 명령어를 통해 dev-dependency 로 chai 를 설치한다.

테스트 파일은 파일명 뒤에 `.specs.js` 를 붙여서 만든다.
간단한 함수를 만들고, 해당 함수에 대한 테스트 파일을 만들어 보자.

```javascript
const test = {};

test.returnHello = () => 'hello';

module.exports = test;

```
*./index.js*

'hello' 라는 문자열을 반환하는 returnHello 함수를 가진 객체를 export 하였다.
test 디렉토리를 생성하고 returnHello 함수를 테스트 해보자.

```javascript
const chai = require('chai');
const { expect } = chai;

const testTarget = require('../test');

describe('testTarget', () => {
  describe('returnHello', () => {
    it('return string hello', () => {
      // Arrange
      const sut = testTarget;

      // Act
      const actual = sut.returnHello();

      // Assert
      expect(actual).to.eql('hello');
    });
  });
});
```
*./test/index.specs*

위 테스트 코드는 단위 테스트의 한 스타일인 AAA(Arrange, Act, Assert) 패턴을 따랐다.
AAA 패턴은 간단히 설명하자면, 준비하고 실행한 결과가 예상치와 같은지를 검사하는 패턴이다.

describe 는 테스트의 범위를 정하는 메소드로, 위의 테스트 케이스에서는 두 번 사용하여 testTarget 객체의 returnHello 함수로 범위를 정하였다.
it 은 실제로 테스트를 실행하는 부분을 설정하는 것이다.
*context 를 사용하여 케이스를 지정할 수 도 있다. 아래에서 좀 더 자세히 알아볼 것이다.* 
chai 의 expect 메소드를 사용하여 반환값이 예상한 결과와 맞는지 확인한다. 

`mocha` 명령어를 실행하면 현재 경로의 test 디렉토리하위의 모든 테스트 파일(.specs.js 로 끝나는)을 실행한다.
만약 한 파일만을 대상으로 테스트를 실행하려면 `mocha 파일경로` 로 실행하면 된다.
실행시 다양한 옵션을 사용할 수 있는데, 필자는 주로 -w 옵션을 사용해 테스트 코드가 변할 때마다 재실행 시켜 변한 값이 테스트 케이스를 통과하는지 확인한다.

### 다양한 테스트 케이스 별 단위 테스트

테스트를 하는 경우는 다양한데, 각 테스트 케이스 별로 어떤 방식으로 테스트 하는지 알아보자.
*이는 필자의 방법으로, 더 좋고 효율적인 방법이 무조건 존재한다. 필자의 방법이라도 도움이 된다면 참고하여 작성해보기 바란다.*

* 비동기 함수를 테스트 하는 경우
* 에러를 throw 하는 경우
* 비동기 함수에서 에러를 throw 하는 경우
* 테스트 대상 함수 안에서 사용하는 다른 함수까지 테스트 할 필요 없을 경우

#### 비동기 함수를 테스트 하는 경우

it() 메소드의 두 번째 인자인 콜백함수를 async 함수로 작성하고, act 할 때 await 한다.

```javascript
test.asyncFunction = () => new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('hello');
  }, 3000);
});
```
*./index.js*

```javascript
describe('asyncHello', () => {
    it('return string hello', async () => {
      // Arrange
      const sut = testTarget;

      // Act
      const actual = await sut.asyncHello();

      // Assert
      expect(actual).to.eql('hello');
    });
  });
```
*./test/index.specs*

#### 에러를 throw 하는 경우

함수에서 에러를 제대로 던지는지 여부를 검사하려면 expect 의 메소드 중 .to.throw 를 사용한다.

```javascript
test.throwError = (shouldThrow = false) => {
  if (!shouldThrow) {
    return 'hello';
  }

  throw new Error('something wrong');
};
```
*./index.js*

```javascript
describe('throwError', () => {
    context('if should throw error is false', () => {
      it('return string hello', () => {
        // Arrange
        const sut = testTarget;

        // Act
        const actual = sut.throwError(false);

        // Assert
        expect(actual).to.eql('hello');
      });
    });

    context('if should throw error is true', () => {
      it('throw Error', () => {
        // Arrange
        const sut = testTarget;

        // Act && Assert
        expect(() => sut.throwError(true)).to.throw('something wrong');
      });
    });
  });
```
*./test/index.specs*

throw case 를 검사할 때는 expect 대상을 this 바인딩을 해 줘야 하므로 화살표 함수를 사용했다.
sut와 수동으로 bind 해줘도 무방하다.

#### 비동기 함수에서 에러를 throw 하는 경우

이 경우에는 chai 의 plug-in 인 chai-as-promised 를 사용한다.
`npm i chai-as-promised --save` 로 설치한다.

```javascript
test.asyncThrowError = (shouldThrow = false) => new Promise((resolve, reject) => {
  setTimeout(() => {
    if (!shouldThrow) {
      resolve('hello');
    }

    reject(new Error('something wrong'));
  }, 1000);
});
```
*./index.js*

```javascript
const chai = require('chai');
const chaiAsPromised = require('chai-as-promised');

const { expect } = chai;
chai.use(chaiAsPromised);

describe('asyncThrowError', () => {
    context('if should throw error is false', () => {
      it('return string hello', async () => {
        // Arrange
        const sut = testTarget;

        // Act
        const actual = await sut.asyncThrowError(false);

        // Assert
        expect(actual).to.eql('hello');
      });
    });

    context('if should throw error is true', () => {
      it('throw Error', async () => {
        // Arrange
        const sut = testTarget;

        // Act && Assert
        await expect(sut.asyncThrowError(true)).to.be.eventually
          .rejectedWith(Error).and
          .has.property('message').with.contain('something wrong');
      });
    });
  });
```
*./test/index.specs*

rejectedWith 메소드를 사용해 어떤 Error 가 던져졌는지 확인할 수 있다.
에러 클래스를 지정해 사용하는 경우 타입 검사까지 할 수 있다.
 
#### 테스트 대상 함수 안에서 사용하는 다른 함수까지 테스트 할 필요 없을 경우

이 경우는 통합 케이스의 경우인데, 단위테스트가 충분히 이루어진 함수에 대해서 리턴값(혹은 throw 값)을 임의로 지정하는 것이다.
sinon 이라는 라이브러리를 사용하며, 설치는 다음과 같이 한다. `npm i sinon --save`

```javascript
test.mocking = () => {
  const helloString = test.returnHello();

  return helloString;
};
```
*./index.js*

```javascript
const sinon = require('sinon');

describe('testTarget', () => {
  afterEach(() => {
    sinon.restore();
  });

  describe('mocking', () => {
    it('return string hello', () => {
      // Arrange
      const sut = testTarget;
      const mock = sinon.mock(testTarget);
      mock.expects('returnHello').once().returns('hello');

      // Act
      const actual = sut.mocking();

      // Assert
      expect(actual).to.eql('hello');
    });
  });
});
```
*./test/index.specs*

mock 을 사용하면 returnHello 함수를 한 번 더 실행하지 않고 실행한 것과 같이 리턴값을 설정 할 수 있다.
afterEach 를 통해 모든 테스트가 끝날 때마다 sinon 을 초기화 하는것도 잊지말자.
*하지 않으면 위 케이스이후 테스트 케이스에서 returnHello 는 모두 mocking 된다.*

## AWS CodeBuild 의 buildspec.yml

> CodeBuild 는 buildspec 파일에 정의된 명령에 따라 빌드한다.

CodeBuild 만 사용하는 경우 buildspec 문서에 실제 deploy 하는 명령까지 포함하지만,
이번 포스트에서는 CodePipeline 을 사용하여 deploy 하는 것으로 가정하고 buildspec 문서를 작성해보겠다.
*CodePipeline 구성은 [이전 포스트 글]({% post_url 2020-12-27-CICD-by-AWS-Code-pipeline %})을 참고하자.*

```yaml
version: 0.1

phases:
  install:
    commands:
      - npm install
  build:
    commands:
      - npm test
artifacts:
  files:
    - '**/*'
```
*./buildspec.yml*

위의 yml commands 는 터미널에서 사용하는 command 를 의미한다.
install 단계 에서는 `npm install` 을 통해 관련 모듈을 설치한다.
build 단계에서는 `npm test` 를 통해 테스트 하고, 모든 테스트 케이스가 성공하면 빌드완료로 가정한다.

npm test 스크립트는 package.json 파일에 정의한다.

```json
{
  "scripts": {
    "test": "mocha"
  }
}
```

## end

모든 테스트가 성공하면 배포가 되는 CI/CD 환경을 구축했다.
