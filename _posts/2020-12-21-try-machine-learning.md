---
title: "Teachable Machine 을 통해 머신러닝 맛보기"
layout: post
categories: [Teachable Machine, machine learning]
---

## summary

> 머신러닝에 대하 아주 간단히 알아본다.
>
> Google Teachable Machine 을 통해 머신러닝을 체험해본다.

## Machine learning?

> 기계 학습(機械學習) 또는 머신 러닝(machine learning)은 경험을 통해 자동으로 개선하는 컴퓨터 알고리즘의 연구이다.

인공지능의 한 분야로, 인간의 뇌가 자연스럽게 수행하는 학습이라는 능력을 컴퓨터로 구현하는 것이다.

일반적으로 머신러닝을 다음의 과정을 거쳐 동작한다.

1. 일정량 이상의 샘플 데이터를 입력한다.
2. 입력 받은 데이터를 분석하여 일정한 규칙과 특징을 찾는다.
3. 찾아낸 패턴과 규칙을 통해 의사결정 및 예측등의 행동을 한다.

그렇다면 어떻게 컴퓨터는 특징과 규칙을 찾아낼까?
그 답은 벡터(vector)에 있다. 벡터는 공간 상에서 크기와 방향을 가지는 것을 의미한다.

아래 그림에서 네모칸에는 어떤 도형이 들어가야 할까?

![predict shape by human](/assets/images/vector1.png)

아마 별 문제 없이 **'별모양'** 이라고 대답할 수 있을 것이다.
이는 사람이 가지고 있는 지각능력을 사용하여 각 모양이 뭉쳐 있는 위치관계를 무의식적으로 파악했기 때문이다.
하지만 컴퓨터는 이런 지각능력을 가지고 있지 않기 때문에, 이 위치관계를 인식할 지표가 필요하다.

이때 활용되는 것이 바로 위에서 언급한 벡터(vector)이다.
위의 그림에서는 별모양과 원모양의 점들이 바로 하나하나의 벡터가 된다.
또한, 특정 벡터들이 모여 있는 것을 특징량이라고 부르며, 머신러닝에서는 바로 이 특징량을 바탕으로 벡터들을 서로 구분한다.

![predict shape by vector](/assets/images/vector2.png)

앞선 그림에 특징량을 바탕으로 이처럼 구분선을 하나 그으면, 컴퓨터도 두 특징량을 쉽게 구분할 수 있게 된다.
머신러닝은 계산을 통해 이러한 구분선을 찾아내는 행위라고도 볼 수 있.

**즉, 벡터를 기반으로 특징량을 추출하고 특징량 사이에 구분선을 지어 의사결정을 한다.**

*reference - [머신러닝부터](https://medium.com/@katekim720/%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D%EB%B6%80%ED%84%B0-20ec2cff05dc), [머신러닝이란?](http://www.tcpschool.com/deep2018/deep2018_machine_learning)* 

## Teachable Machine

> Train a computer to recognize your own images, sounds, & poses.

Teachable Machine 은 웹에서 쉽게 머신러닝을 하고, export 하여 web, app 등에서 사용할 수 있도록 하는 프로젝트이다.

Teachable Machine 에서 기계에게 학습시킬 수 있는 것들은 아래와 같다.

* 이미지
* 소리
* 포즈

[Teachable Machine](https://teachablemachine.withgoogle.com/)에 접속해 간단히 이미지 머신러닝을 체험해보자.
설명을 위해 동물 사진을 업로드 하면 해당 동물이 돼지인지, 고양이인지 판단하는 프로젝트를 만들어보자.

**'Get started'** 버튼을 눌러 프로젝트를 생성한다.

![select project type](/assets/images/teachable-machine-select-project-type.png)

다음 화면에서 프로젝트의 타입을 선택할 수 있는데, image project 를 선택한다.

![learning by classs](/assets/images/learning-by-class.png)

각 클래스명을 Cat, Pig 으로 편집한 후, image sample 을 충분히 업로드한다.
필자는 고양이와 돼지 각각 10장의 사진을 업로드 하였다.

![upload sample images](/assets/images/upload-images.png)

Training 버튼을 눌러 고양이와 돼지에 대한 이미지를 머신러닝 시킨다.
머신러닝하는 중이라는 프로그레스 바가 다 차게 되면, 오른쪽에 input 을 할 수 있는 창이 활성화 된다.
select box 를 file 로 변경한 후, 학습에 사용되지 않은 고양이 사진을 한 번 업로드 해보자.

![predict](/assets/images/predict-by-photo.png)

고양이 100% 로 결과가 나왔다.

## end

머신러닝에 대한 간략한 이해와 Teachable Machine 을 통해 머신러닝을 체험해 보았다.
다음 포스트에서는 Teachable Machine 의 결과를 model 로 export 해서 사용하는 방법에 대해서 알아보도록 하겠다.



 
