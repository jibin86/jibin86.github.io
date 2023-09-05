---
title:  "[논문 리뷰] DDPM: Denoising Diffusion Probabilistic Models"
categories: [AI Tech, Computer Vision, Paper Review]
tags: [diffusion]
---   

오늘은 Diffusion의 기본 논문인 DDPM 논문을 공부해보고자 한다.  

# 1. Abstract & Introduction

![Untitled](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/a705446f-bb84-4c7f-904e-8fa64fdce7dd)

- 이 논문은 높은 해상도의 이미지 생성하는 방식을 제안한다.
- Diffusion(확산)이라는 아이디어는 비평형 열역학에서 가져온 개념이다.

<br>

## 1.1. Diffusion Probabilistic Model (DPM)

- Diffusion Probabilistic Model은 **Variational inference(변분추론)**를 사용하여 sample를 생성하는데, 생성된 sample들이 **Diffusion Process** 과정의 데이터와 일치하도록 훈련된다. 
즉, Diffusion Process를 **Reverse**하도록 훈련된다. (노이즈 입히는 과정을 학습하여 그대로 노이즈를 제거할 수 있도록 한다.)
    - **Diffusion Process**
        - **Diffusion Process**는 데이터에 점진적으로 노이즈를 넣는 과정이며, 완전한 가우시안 노이즈가 될 때까지 노이즈를 추가한다.  
        
        <br>

    - **Variational Inference**
        - **Variational Inference**이란, 확률적 모델에서 숨겨진 latent 변수를  추론하고, 모델의 사후 분포(posterior)를 근사화하는 기법이다.
            - 아래와 같이 사후 분포를 변분 추론 방식이 아닌, **베이즈 정리**를 사용해서도 표현할 수 있다.
                - **베이즈 정리**란, 주어진 데이터를 기반으로 모수의 조건부 확률분포를 계산하는 작업이다.
                    - 사후 분포 (posterior; $P(θ\|D)$) ∝ 가능도 (likelihood; $P(D\|θ)$) x 사전 분포 (prior; $P(θ)$)
                        - 가능도(likelihood; $P(D\|θ)$): 모델 파라이터가 특정한 값으로 주어졌을 때, 주어진 데이터 $D$가 나올 확률값을 나타낸다. 데이터가 모델에 얼마나 적합한지를 나타낸다.
                        - 사전 분포(prior; $P(θ)$): 숨겨진 변수에 대한 초기 추정치
                        - 사후 분포(posterior; $P(θ\|D)$): 주어진 관측 데이터 $D$를 바탕으로 모델 파라미터 $θ$의 확률 분포를 나타내는 것
            - **하지만 사후 분포를 직접 계산하기 어려울 때가 많다.** ⇒ 따라서 변분 추론을 사용하여 사후 분포를 간단하고 추론 가능한 분포로 근사화할 수 있다.
            - 변분 추론의 **최적화**: 변분 추론 방식은 근사 분포와 실제 사후 분포 사이의 거리를 최소화하는 방향으로 모델 파라미터를 조정한다.  

<br>

- 마지막으로 Diffusion 모델은 다른 기존의 생성모델(GANs, VAE 등)보다 hight quality sample를 생성할 수 있다.

<br>

## 1.2. GAN vs VAE vs Diffusion 비교

![Untitled 1](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/cf073904-71ab-4c2e-a837-92a8ed8937b3)

- GAN
    - high quaility samples(좋은 화질), Fast Sampling(빠르다).
    - 다양한 결과를 얻기 어렵다.
- VAE
    - Fast Sampling(학습 속도가 빠르다), Mode Coverage/Diversity(다양한 샘플을 잘 만든다. training 분포를 잘 따라간다.)
    - 생성된 이미지의 품질이 낮다.
- Diffusion
    - high quaility samples(좋은 화질), Mode Coverage/Diversity(다양한 샘플을 잘 만든다. training 분포를 잘 따라간다.)
    - 느리다

<br>

## 1.3. Result

- CIFAR 10 데이터셋을 사용해서, Inception score은 9.46, FID score는 3.17으로 SOTA를 달성하였다.
    - **Inception score**
        - 이미지 생성 모델이 다양하고 구별 가능한 이미지를 생성하는 능력을 평가하는 척도이다.
        - $IS = \exp( E[ KL(p(y\|x) \|\| p(y)) ] )$
            - x는 생성된 이미지, y는 클래스를 나타낸다.
            - $p(y\|x)$는 사전 훈련된 분류 모델이 이미지 $x$가 클래스 $y$에 속할 확률 분포를 나타낸다.
            - $p(y)$는 이미지 $x$가 클래스 $y$에 속할 확률 분포,
            - $KL(·)$은 쿨백-라이블러 발산(Kullback-Leibler Divergence)를 의미하며 분포가 얼마나 차이가 나는지를 계산한다.
        - 높은 Inception Score는 다양하고 질 높은 이미지를 생성하는 모델을 나타내며, 낮은 Inception Score는 일반적이지 않거나 구별하기 어려운 이미지를 생성하는 모델을 나타낸다.
    - **FID score**
        - 이미지 생성 모델이 실제 데이터 분포와 얼마나 유사한지를 측정하는 척도이다. (생성된 이미지와 실제 이미지 간의 차이를 측정)
        - $FID = \|\| μ_g - μ_r \|\|^2 + Tr( Σ_g + Σ_r - 2 * (Σ_g * Σ_r)^0.5 )$
            - $μ_g$와 $μ_r$는 각각 생성된 이미지와 실제 이미지의 평균을 나타낸다.
            - $Σ_g$와 $Σ_r$은 각각 생성된 이미지와 실제 이미지의 공분산 행렬을 나타낸다.
            - $\|\|·\|\|$는 벡터의 L2 norm을 의미한다.
            - $Tr(·)$은 행렬의 trace(대각합)를 의미한다.
        - 낮은 FID는 생성된 이미지와 실제 이미지 간의 차이가 적어서 더 좋은 모델을 나타낸다.

<br>

# 2. Background

- Diffusion 모델은 Generative model로서 학습된 데이터의 패턴을 생성해내는 역할을 한다.
- 목표: 간단한 분포(가우시안 분포)를 특정한 패턴을 갖는 분포로 변환한다.
- 패턴 생성 과정을 학습하기 위해 고의적으로 패턴을 무너뜨리고**(Diffusion process)**, 
이를 다시 복원하는 과정을 학습한다**(Reverse process)**.
- Diffusion models는 크게 이미지에 노이즈를 더하는 **Forward process**(=Diffusion process)와 노이즈를 제거하여 이미지를 생성하는 **Reverse process**로 구성되었다.
- 이미지 수식
    
    $X_T$: 노이즈로 가득찬 이미지
    
    $X_0$: 노이즈가 없는 이미지
    
<br>

## 2.1. Diffusion process (forward process)

- 이미지에 노이즈를 더해주는 과정이다.
- $q(x_t\|x_{t-1})$: $x_{t-1}$이 주어지면, $x_t$가 나온다. 어떤 이미지를 노이즈화하는 확률 분포이다.

<br>

## 2.2. Denoising process (reverse process)

- 이미지에 노이즈를 빼주는 과정이다.
- 노이즈를 제거하는 과정은 여러 스탭(t)으로 쪼개져, Markov Chain으로 구성되어있다.
    - DDPM에서는 1000번의 스탭으로 쪼갰다.
    - Markov chain
        - Markov 성질을 갖는 이산 확률과정
            - Markov 성질: “특정 상태의 확률(t+1)은 오직 현재(t)의 상태에 의존한다.
                - $P[s_{t+1}\|s_t]=P[s_{t+1}\|s_1,\dots,s_t]$
            - 이산 확률과정: 이산적인 시간(0초, 1초, 2초, ..) 속에서의 확률적 현상
- $q(x_{t-1}\|x_t)$: $x_t$가 주어지면, $x_{t-1}$이 나온다. 노이즈로부터 이미지를 만드는 과정이다.
- 모델이 하고자 하는 것:
    - 노이즈가 들어왔을 때, 노이즈를 어떻게 뺄 것인지 학습하여 $x_0$를 만든다.
        
        ![Untitled 5](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/229e3951-d6b5-4fa1-bef4-93c35b2fc2c7)

<br>        

# 3. Forward process

- 이미지에 노이즈를 더해주는 과정이다.
- $q(x_t\|x_{t-1})$: $x_{t-1}$이 주어지면, $x_t$가 나온다. 어떤 이미지를 노이즈화하는 확률 분포이다.
    - $x_t\|x_{t-1} \sim N(\sqrt{1-\beta_t}x_{t-1}, \sqrt{\beta_t}^2I)$
- 어떻게 이미지에 노이즈를 더해줄까 ⇒ 베타 스케줄링을 통해 노이즈의 정도를 조절하여 더해준다.
    - Beta scheduling
        
        ![Untitled 6](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/98823e23-e2c1-4f7a-b55f-8943574adfda)

        
    - 베타 스케줄링을 적용하기 전해 이미지에 노이즈를 어떻게 추가하는지 이해하기 위해서 단순히 noise만 추가한 수식을 살펴보자.
        
        <br>

    - $x_{t-1}$에서 단순히 noise를 추가한 $x_t$를 만드는 수식은 다음과 같다.
        
        $x_t = x_{t-1} + \epsilon$
        
        $X_{t-1}\sim N(\mu,\sigma)$ 이고, $\epsilon \sim N(0,1)$ 라고 하자. ($X_t$ 와 $\epsilon$ 은 독립이다.)
        
        단순히 noise가 추가된 $X_t$ 의 평균과 분산을 구해보자.
        
        $E(X_t) = E(X_{t-1}) +E(\epsilon)$  
        $=\mu+0$  
        $= \mu$
        
        $Var(X_t) = Var(X_{t-1}+\epsilon)$  
        $= Var(X_{t-1}) + Var(\epsilon)$  
        $= \sigma^2 + 1^2 = \sigma^2+1$  

        <br>
        
    - 우리는 여기서 data에 noise를 점진적으로 추가하고 싶기 때문에, variance schedule $\beta_1, ... , \beta_T$ 를 이용하여 noise를 scaling을 한 후 더해준다.
        
        $x_t = x_{t-1} + \beta_t\*\epsilon$  
        
        $E(X_t) = E(X_{t-1}) +E(\beta_t\*\epsilon)$  
        $=\mu+\beta_t*0 = \mu$  
        
        $Var(X_t) = Var(X_{t-1}+\beta_t\*\epsilon)$  
        $= Var(X_{t-1}) + Var(\beta_t*\epsilon)$  
        $= \sigma^2 + \beta_t^2$  
        
        $X_t \sim N(\mu, \sigma^2 + \beta_t^2)$  

        <br>
        
    - 이때, 노이즈가 추가되면서 variance가 발산하는 것을 막기 위해 $x_{t-1}$ 에 $\sqrt{1-\beta_t}$ 를 곱해준다.
        
        $x_t = \sqrt{1-\beta_t}\*x_{t-1} + \beta_t*\epsilon$  
        
        $E(X_t) = E(\sqrt{1-\beta_t}\*X_{t-1}) +E(\beta_t\*\epsilon)$   
        $=\sqrt{1-\beta_t}\*\mu+\beta_t\*0$   
        $= \sqrt{1-\beta_t}\*\mu$  
        
        $Var(X_t) = Var(\sqrt{1-\beta_t}\*X_{t-1}+\beta_t\*\epsilon)$    
        $= Var(\sqrt{1-\beta_t}\*X_{t-1}) + Var(\beta_t\*\epsilon)$    
        $= \sqrt{1-\beta_t}^2\sigma^2 + \beta_t^2$ 

        <br>
        
    - 따라서 $X_t$ 의 분포는 다음과 같은 분포를 따른다.
        
        $X_t \sim N(\sqrt{1-\beta_t}*\mu, \sqrt{1-\beta_t}^2\sigma^2 + \beta_t^2)$ 

<br>
        
- 위의 하나씩 노이즈를 추가하는 단계를 $x_0$에서 $x_t$까지 한번에 부여하면 다음과 같은 수식으로 나타낼 수 있다.
    
    $x_t = \sqrt{\bar{\alpha_t}}\*x_{0} + \sqrt{1-\bar{\alpha}}*\epsilon$
    
    ![Untitled 7](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/aad66884-7e01-47ed-9120-2b9db383389f)

<br> 

# 4. Reverse process

- 이미지에 노이즈를 빼주는 과정이다.
- $q(x_{t-1}\|x_t)$: $x_t$가 주어지면, $x_{t-1}$이 나온다. 노이즈로부터 이미지를 만드는 과정이다.
    - $x_t\|x_{t-1} \sim N (x_{t−1}; μ_θ (x_t, t), Σ_θ (x_t, t))$
- 방식
    - $q(x_{t-1}\|x_t)$ 를 예측하는 것이다. ⇒ $p_\theta(x_{t-1}\|x_t)$
    ( ⇒  $x_t\|x_{t-1}$ 의 분포를 예측하는 것이다.)
    - 분포를 예측한다는 의미는 해당 분포의 평균과 분산을 예측한다는 의미이다. 
    따라서 $μ_θ (x_t, t), Σ_θ (x_t, t)$ 을 예측할 것이다.
- Loss
    
    $μ_θ (x_t, t), Σ_θ (x_t, t)$ 를 예측하기 위해 아래와 같은 Loss로 모델을 학습한다.
    
    - 아래의 Loss는 가우시안 분포들을 비교하는 KL-Divergence로 이루어져있다.
        
        ![Untitled 8](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/0771aff1-b1b1-426a-8b91-0564c311359c)

        
        - KL divergence를 사용하여 forward $q(x_{t-1}\|x_{t})$과 reverse $p_\theta(x_{t-1}\|x_t)$의 분포를 비교한다.
        - x0가 주어진다면 아래와 같이 $q(x_{t-1}\|x_{t})$를 계산할 수 있다.
            
            ![Untitled 9](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/9d0c370b-4947-4183-8f5a-bc49f1690f7d)

<br>            

# 5. DDPM Loss

![Untitled 10](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/d85471f0-7dfb-4e06-a5b6-02e70a15073a)

<br>

## 5.1. VAE Loss

![Untitled 11](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/fe058fad-42e9-4e4e-b47c-e0492520eecf)

위의 Loss는 VAE의 Loss이다. 

- Regularizer on Encoder
    - KL-Divergence Loss는 잠재벡터 z의 분포를 N(0,1)와 비교하여 정규화해주는 역할을 한다.
        - KL Divergence
            
            KL(Kullback-Leibler) divergence
            
            : 확률 분포 간의 차이를 측정하는데 사용되는 개념
            
            $KL(P\|\|Q)=\sum[P(x)\log{P(x) \over Q(x)}]$
            
            P(x)와 Q(x)는 각각 확률 분포 P와 Q에서 사건 x가 발생할 확률
            
            P(x)는 희망하는 타겟에 대한 결괏값이고, Q(x)는 모델에서 출력한 출력값
            
            값이 0에 가까울 수록 두 분포가 유사하다는 의미이다.
            
            KL divergence는 분포 간의 차이를 측정하는 값으로 활용되며, 두 분포의 유사성을 평가하거나 모델의 학습에서 분포를 근사하는데 사용될 수 있다.
            
            ![Untitled 12](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/c045c5ad-ed73-43b3-80a5-2f298a1760f7)

            
            - 로그를 씌우는 이유
                - 로그 함수인 log(P(x) / Q(x))를 적용함으로써, 분포 P와 Q의 확률 값의 비율을 계산한다. 이 비율은 P(x)가 Q(x)에 비해 얼마나 더 큰지 또는 작은지를 나타내는데, 로그 함수를 적용함으로써 이 차이를 상대적으로 강조할 수 있다.
                    
                    로그 함수의 특성 중 하나는 입력 값이 1보다 크면 양수, 1보다 작으면 음수로 변환되는 것이다. 따라서 P(x) / Q(x)의 값이 1보다 크면 로그 값은 양수로 나타나고, 이는 P(x)가 Q(x)에 비해 상대적으로 더 크다는 것을 의미한다. 반대로 P(x) / Q(x)의 값이 1보다 작으면 로그 값은 음수로 나타나고, 이는 P(x)가 Q(x)에 비해 상대적으로 더 작다는 것을 의미한다.
                    
                    따라서 KL divergence를 통해 두 분포의 차이를 평가할 때 로그 함수를 적용함으로써 상대적인 차이를 강조할 수 있다.
                    
            - 로그 앞에 P(x)를 곱하는 이유
                - P의 중요한 사건에 더 큰 가중치를 부여한다는 의미로, P(x)가 낮은 확률로 발생하는 사건에 대해 정보의 손실이 상대적으로 작아지며, P(x)가 높은 확률로 발생하는 사건에 대해 정보의 손실이 상대적으로 커지는 효과를 가지게 된다.
                    
                    따라서 KL divergence는 상대적인 중요도와 차이를 동시에 고려하는 측도로서 활용될 수 있다.

<br>
                    
- Reconstruction on Decoder
    - Reconstruction Loss는 z에서 원본 이미지를 생성하고, 정답 이미지와 비교하여 근접하게 만들어주는 역할을 한다.

<br>

## 5.2. Diffusion Loss ⇒ DDPM Loss

![Untitled 13](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/6a6e79c1-4f1e-43bd-9455-7363baf5074c)

녹색 부분이 Diffusion 모델의 핵심이다.

DDPM에서는 Diffusion Loss가 간단해졌다.

<br>

### 5.2.1. Regularization term 제외

- Regularization term: $x_T$ (T 시점의 Latent Variable)가 표준정규분포를 따르도록하는 역할을 한다.
- 하지만 1000번의 step에 걸쳐 노이즈를 주입해보니까, Regularization term을 이용해서 $\beta_t$ 를 학습하지 않더라도 $x_T$ 이 아래와 같이, 표준정규분포와 매우 유사하게 형성된다는 것을 알게되었다.      
    ![Untitled 14](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/55980854-62c7-4458-b1d3-28e5c7b4e46e)

<br>    

### 5.2.2. Denoising Process의 재구성

- Denoising process Loss: $x_0$와 $x_t$ 가 주어졌을 때, $x_{t-1}$ 의 분포와 사전에 정의한 $x_t$가 주어졌을 때, $x_{t}$ 의 분포를 비교하여 근접해지도록하는 역할을 한다. 하지만 사전에 해당 분포를 어떻게 적용하지?
- 이전에 Denoising 과정에서 **각 시점의 평균과 분산을 학습**해야한다고 했었다.
    
    ![Untitled 15](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/5d656a21-6efe-4f33-af1a-f0795490ff65)

    
    - $\mu_\theta(X_t,t)$ 와, $\Sigma_\theta(X_t,t)$ 는 각각 $p_\theta(X_{t-1}\|X_t)$ 분포의 평균과 분산을 의미하며, $X_t$ 와 t(시점)에 따라서 각각 달라진다.

<br>

- DDPM에서는 분산에 해당하는 $\sum_\theta(X_t,t)$ 을 상수화하였다.
    - 위의 Regularization term을 제외하는 부분에서, $\beta$ 는 미리 사전 정의된 스케줄에 따라서 결정되기 때문에 time dependent한 상수로 처리하기로 하였다. 따라서 분산은 상수화되어 학습할 필요가 없어졌다.
        - $\sigma^2_t = \tilde{\beta_t} =$ t 시점까지의 누적된 noise              
            ![Untitled 16](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/1a0754e5-aaeb-4951-ba45-2c59afa39a77)              
        
        ![Untitled 17](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/26996f21-2d4a-4508-b251-d6d74e5f2f4f)

<br>

- 분산을 학습할 필요가 없기 때문에 평균만 학습할 수 있는 Loss로 재구성하였다.      
    ![Untitled 18](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/308a9316-e5fa-401b-933f-bcae538af1a6)

    
    - 둘의 mean function 간의 차이로 계산할 수 있다.      
        ![Untitled 19](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/a1e29d69-f390-4513-8bef-c5478955ad08)

    
    하지만 위의 평균을 봤을 때, 우리가 모르는 값은 $\epsilon$ 이다.
    
    따라서 Loss function을 다음과 같이 $\epsilon$ 을 구하는 과정으로 수정할 수 있다.      
    ![Untitled 20](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/c30acf36-d8a5-4e8d-8e79-aa79dccfa8ab)

    
    따라서 평균을 구성하는 입실론을 예측하는 것으로 간소화 할 수 있다.      
    ![Untitled 21](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/b6716c9b-3880-482d-99cf-6a0d9b93e83a)

    
    위의 수식에서 입실론에 대한 MSE와 노이즈와 관련된 계수가 붙어있음을 알 수 있다.
    
    그런데 DDPM은 여기서 계수 부분을 제거하여 Loss를 더 간소화 했다.      
    ![Untitled 22](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/12b864cc-c19f-4ad4-9d24-5e1e946b141b)

    
    ⇒ 이 Loss을 하이레벨로 설명하면 결국, 노이즈 예측을 목적으로 한다는 것이다.
    따라서 노이즈 예측을 목적으로 한다! 라는 Denoising matching이 DDPM의 가장 중요한 Contribution이 된다.
    
<br>

# 6. Experiments

- forward
    - T = 1000으로 설정
    - forward process의 variances를 $β_1 = 10^{−4}$ 에서 $β_T = 0.02$ 으로 linearly하게 설정
- reverse
    - reverse process는 U-Net을 백본으로 사용
    - Transformer sinusoidal position embedding을 통해서 t에 따라서 파라미터가 공유될 수 있다.
    - 16x16 feature map self-attention 사용

<br>

## 6.1. Sample quality

![Untitled 23](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/b753c7ef-448d-4207-9033-21f85fd36ab7)

- CIFAR10 데이터 셋에 대한 성능 결과이다.
    - Uncoditional 부문에서 Ours의 FID score가 3.17로 가장 작으며, Sample quality가 가장 높다.

<br>

## 6.2. Reverse process parameterization and training objective ablation

- variances를 fixed한 것보다 variances를 학습하는 것이 불안정하고, 더 좋지 않은 퀄리티를 나타낸다.
- $\epsilon$ 를 예측하는 것이 $\tilde{\mu}$ 를 예측하는 것과 거의 같은 효과를 내고, 간소화된 Loss에서 훈련하면 훨씬 좋은 성능을 낸다.

<br>

## 6.3. Progressive coding

- lossy compressors로써 high quality를 낼 수 있다.
- a rate of 1.78 bits/dim ⇒ Rate는 데이터를 표현하기 위해 사용되는 비트의 수
- a distortion of 1.97 bits/dim ⇒ Distortion은 압축된 데이터가 원래 데이터와 얼마나 다른지 나타냄
- Rate-distortion이란 (**feat. chat gpt**)
    
    Rate-distortion은 정보 이론(Information Theory)과 신호 처리(Signal Processing) 분야에서 사용되는 개념으로, 데이터의 압축(compression)과 왜곡(distortion) 사이의 트레이드오프를 나타내는 개념입니다.
    
    정보 이론에서 Rate는 데이터를 표현하기 위해 사용되는 비트의 수를 나타내며, Distortion은 압축된 데이터가 원래 데이터와 얼마나 다른지를 나타냅니다. Rate-distortion trade-off는 이 두 개념 사이의 관계를 설명하는 것으로, 데이터를 더 효과적으로 압축하기 위해서는 정보 손실(왜곡)을 허용해야 한다는 원리를 의미합니다.
    
    Rate와 Distortion 사이의 관계는 일반적으로 다음과 같이 나타낼 수 있습니다. 여기서 R은 Rate, D는 Distortion을 나타냅니다.
    
    R(D) = 최소 Rate를 가지면서 Distortion D 이내에서의 압축
    
    즉, Rate-distortion trade-off는 더 낮은 Rate로 더 좋은 압축을 달성하려면 Distortion을 허용해야 한다는 원리를 나타냅니다. 일반적으로 압축 알고리즘이 Rate를 줄이면서 데이터의 왜곡을 허용하도록 설계되며, 이로 인해 데이터가 압축되면서 일부 정보 손실이 발생할 수 있습니다.
    
    Rate-distortion trade-off는 데이터 압축에 있어서 중요한 개념으로 사용되며, 다양한 압축 알고리즘과 기술에서 적용됩니다. 데이터를 효과적으로 압축하면서도 필요한 수준의 왜곡을 유지하는 것은 실제 응용에서 중요한 문제이며, Rate-distortion trade-off를 고려하여 적절한 압축 방법을 선택하고 최적화하는 것이 필요합니다.
    
<br>

### 6.3.1. Progressive lossy compression

![Untitled 24](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/e56cc56e-3f5f-478e-8244-9c568eccd1ad)

- 낮은 rate로 낮은 distortion 가능하다. 즉 적은 비트를 사용해도 정보의 손실이 크지 않음을 의미한다.

<br>

### 6.3.2. Progressive generation

- reverse process sampling을 통해 x0를 예측함
- x0를 예측하기 위한 reverse process의 모습
    - 큰 규모의 이미지 feature들은 초반에 모습을 드러내고, 이후에 뒷부분에서 세부적인 디테일들이 나타난다.          
        ![Untitled 25](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/044363b8-f151-4e36-a758-1b036a4bf01d)          
        ![Untitled 26](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/7ed1ddcb-2533-4209-9707-91a6861145d2)

        
- 아래는 $x_0 ∼ p_θ(x_0\|x_t)$ 를 확률적으로 예측한 이미지이다.      
    ![Untitled 27](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/6ce1ef98-5221-480d-b246-143813103ff9)      
    - 첫 번째 이미지는 x_1000이 주어졌을 때, 예측된 이미지, 
    두 번째 이미지는 x_500이 주어졌을 때, 
    세 번째 이미지는 x_250이 주어졌을 때, 
    네 번째 이미지는 x_0이 주어졌을 때, 예측된 이미지이다.
    - t가 작을 수록 세부적인 부분을 제외한 전체적인 부분이 보존되고, t가 클 수록 큰 규모의 feature만 보존된다.

<br>

# 7. Conclusion

지금까지 diffusion model를 사용하여 high quality image sample 생성에 대해 알아봤다.

diffusion models은 이미지 생성과 관련된 패턴과 구조를 더 잘 이해하고 학습할 수 있으며, 이미지 생성 분야에서 탁월한 성능을 발휘한다.


추가적으로 아래 코랩 코드는 ddpm 코드는 아니지만 diffusion에 관한 베이직한 부분을 이해하기 위해 작성해봤다.
    
    [Google Colaboratory: https://colab.research.google.com/drive/1ToOq0HatM0fyDG29ikeSHpWebc1q9Lxp?usp=sharing](https://colab.research.google.com/drive/1ToOq0HatM0fyDG29ikeSHpWebc1q9Lxp?usp=sharing)
    

# Reference

[https://arxiv.org/pdf/2006.11239.pdf](https://arxiv.org/pdf/2006.11239.pdf)  
[[Paper Review] Denoising Diffusion Probabilistic Models: https://www.youtube.com/watch?v=_JQSMhqXw-4](https://www.youtube.com/watch?v=_JQSMhqXw-4)