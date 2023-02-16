---
title:  "[Amazon] AWS Deepracer(1) - AWS Deepracer Overview"
categories: [AI Tech, Reinforcement Learning]
tags: [Deepracer]
---

이 포스트는 [AWS Deepracer 강의](https://dongorae.github.io/aws-deepracer-school/Basic/AWS_Deepracer_Overview/09_Deepracer_%EC%BD%98%EC%86%94_%EB%91%98%EB%9F%AC%EB%B3%B4%EA%B8%B0.html)를 바탕으로 공부한 내용을 정리하였습니다.


# 1. DeepRacer란

- 강화학습 자율 주행 애플리케이션을 실험하고 구축할 수 있는 통합 학습 시스템이다.
    - 구성요소
        - AWS DeepRacer 콘솔
            - AWS 서비스를 웹사이트를 통해 작업하는 공간
        - AWS DeepRacer 차량
        - AWS DeepRacer League
- 무엇을 배울 수 있을까
    - AWS
        - Cloud Computing
    - Deep
        - AI, Deep Learning, Reinforcement Learning
        - Python, Tensorflow
    - Racer
        - Autonomous Driving, Robot Operating System
        - Ubuntu Linux, Docker

# 2. 클라우드 컴퓨팅과 AWS

## 클라우드 컴퓨팅이란

- 인터넷을 사용해서 공유자원(서버, 네트워크, 스토리지)을 사용할 수 있는 서비스이다.
- IT에 관련한 물리적 자원 및 기술적 자원을 임대하고 사용한 만큼 금액을 지불한다.

## On Premise vs On Demand

- On-Premise: 서버, 데이터베이스, 네트워크 장비 등을 모두 구매해서 구축하고 운영하는 모델
- On-Demand: 클라우드 컴퓨티 서비스 사용자가 요청한 만큼 서비스를 제공하고 비용을 청구하는 모델

# 3.  Deepracer 워크플로우

- Hubman Driving(simulation→ real)
- Deepracer Driving(simulation → real)
- League

# 4. Deepracer 작동 원리

- Deepracer는 강화학습으로 모델을 학습시킨다. ex_알파고
    - 강화학습을 이해하면 Deepracer가 재미있을 수 있다. 강화학습 공부해보자

## AI

- Rule-based (규칙기반) 규칙과 패턴을 사람의 경험 등에 의해 입력
- Machine Learning (기계학습) 규칙과 패턴을 데이터를 통해 학습
    - Supervised Learning (지도학습) **정답을 예측**하는 모델 y=f(x) 제작
        - 데이터: (x1, y1), (x2, y2), …
        - 학습 방법: 입력(X)에 대한 정답(y)의 관계 학습
    - Unsupervised Learning (비지도학습) 데이터 내부의 **특징**, **패턴**을 추출
        - 데이터: x1, x2, x3, …
        - 학습 방법: 군집, 차원축소, 연관성 분석 등에 따라 상이함
    - Reinforcement Learning (강화학습) **보상이 큰 행동(a)를 예측**하는 모델  a=π(s) 제작
        - 데이터: (s1, a1, r1), (s2, a2, r2), …
        - 학습 방법: 상태(s)에 대한 행동(a)을 보상(r)에 의해 강화

## Model : a=π(s)

- 상태(s) =(a=π(s))⇒ 행동(a) ex_speed, steering angle

## Reward

- 보상(reward)이 높은 행동은 강화하고, 낮은 행동은 약화한다.
- 보상은 사람이 줘야한다. 자동화 함수를 만든다. reward function

## Frame Rate

- 1초에 10번의 프레임을 찍고 행동을 한다.

## Trial and error

- 초반에 시행착오를 정말 많이 한다. ⇒ 컴퓨팅 자원을 많이 사용하는 단점이 있다.
- 사람이 걷는 방식으로 걷지 않아도 더 빠르게(보상에 따라 달라짐) 걸을 수 있다.
    - 정답을 알려주지 않아도 알아서 정답을 찾을 수 있다.
    - 사람보다 더 똑똑해질 수 있다.

# 5. Deepracer 요금 정책

- Free Tier
    - 훈련, 평가 시간: 10시간
    - Storage: 5GB
    - 서비스를 처음 사용한 날로부터 30일 동안 유효
- Service Price
    - 훈련, 평가 시간: 1시간에 3.5$
    - Storage: 한달에 0.023$
- Device Price
    - 꽤 비쌈
    
    [AWS DeepRacer - Fully autonomous 1/18th scale race car for developers | With open source projects](https://www.amazon.com/AWS-DeepRacer-Fully-autonomous-developers/dp/B07JMHRKQG?th=1)
    

# 6. AWS 계정 설정

- 계정 탈취 당하면 막대한 비용이 발생하니 조심 또 조심하자!

## account type

![image](https://user-images.githubusercontent.com/89712324/219346707-f43fa467-5260-4f91-a1ea-4c21990311d0.png)

- Root user
    - 최초 가입한 계정으로 모든 권한을 가진 사용자
    - 매우 주의를 기울여야하고, 가급적 사용하지 않는 것을 권장한다.
- IAM user
    - ROOT 계정에 소속된 보조 계정
    - 일반적으로 필요한 권한만 부여하기 때문에 탈취당하더라도 덜 위험하다.
- Multi-User
    - Deepracer 서비스에서만 존재하는 유저
    - 하나의 IAM 계정에 달라붙어서 여러 유저가 Deepracer를 사용할 수 있도록 만든 계정
    - 학생들이 Deepracer를 사용할 수 있도록 교육용 계정으로 많이 사용된다.

# 7. 딥레이서 알고리즘

- Actor - Critic 기반 알고리즘인 PPO와 SAC 알고리즘을 사용한다.
    - 딥레이서는 PPO 알고리즘이 더 맞는다.
- on policy: 과거 데이터를 버리고 현재 데이터만 사용하여 학습
- off policy: 업데이트할 때 과거 데이터들 중에서 쓸만한 데이터를 뽑아 학습

## PPO

on policy 이지만 약간의 off policy 성향이 있다.  

업데이트할 때 과거 데이터들 중에서 쓸만한 데이터를 뽑아 학습한다.

### Training과 Evaluation 차이

Training할 때 시뮬레이션 한 결과 데이터를 이용해 학습해나간다.

Evaluate할 때 시뮬레이션 한 결과 데이터로는 학습하지 않고 시뮬레이션 결과만 확인한다.

# 8. 탐험

- 목적: 더 좋은 Action을 찾기 위해 기존의 정책에서 벗어난 새로운 Action을 취해보며 탐색하자
- 지나치게 많은 탐색은 학습 속도를 저하시키며, 지나치게 적은 탐색은 더 좋은 Action을 찾을 수 있는 기회를 놓칠 수 있다.

## 탐험 하이퍼파라미터

- PPO - Entropy
    - 기본값 Entropy 0.01
- SAC - alpha value
- 지역 국소점에 빠지지 않게 하기 위해 분산을 너무 낮추면 안된다.
- 장점: 다양한 행동을 장려한다.
- 단점: 다양한 행동을 하기 때문에 학습 속도가 느려진다.
- 초반에는 엔트로피를 높이고 학습하면서 점차 줄여가는 방법도 있다.