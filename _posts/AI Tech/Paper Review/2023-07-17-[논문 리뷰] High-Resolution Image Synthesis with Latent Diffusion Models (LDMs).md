---
title:  "[논문 리뷰] LDMs: High-Resolution Image Synthesis with Latent Diffusion Models"
categories: [AI Tech, Computer Vision, Paper Review]
tags: [diffusion]
---   

[https://arxiv.org/pdf/2112.10752.pdf](https://arxiv.org/pdf/2112.10752.pdf)

이 논문은 2022년도 CVPR에 등재된 논문이다. Diffusion은 컴퓨팅 연산이 많이 필요하며, 단순히 원본 이미지를 복원하는 것보다, Text를 통해서 어떻게 이미지를 생성, 수정할 수 있는지, 원리가 궁금하였기에 이 논문을 찾아 읽게 되었다.  

이 논문은 직접적인 Editing 과정을 설명하진 않지만, 매우 큰 컴퓨팅 연산을 필요로하는 기존의 diffusion 모델을 개선하고, text를 기반으로 이미지를 생성한다는 점에서 공부할 부분이 많고, 흥미로웠기에 이 논문을 선정하여 읽게 되었다.

<br>

# 1. Abstract

기존의 diffusion 모델은 기존의 데이터의 크기를 그대로 유지하며, 픽셀 단위로 가우시안 분포를 학습하는 모델이다. 따라서 복원되는 이미지의 퀄리티는 좋지만 픽셀 단위로 연산을 하기 때문에 매우 큰 컴퓨팅 연산이 필요하다.

이런 단점을 보완하기 위해, 제한된 컴퓨팅 자원으로 Diffusion 모델의 퀄리티와 유연성을 유지하도록하는 사전학습된 AutoEncoder를 적용하였다.

또한 cross-attention layer를 도입하여 text, bounding box, 고해상도 합성 등과 같은 조건적인 입력에 대해서도 생성할 수 있도록 모델을 업그레이드하였다.

이 모델은 다양한 task에 대해서 이미지 inpainting, class-conditional image synthesis, unconditional 이미지 생성, super-resolution 등  SOTA를 달성하였다.

<br>

# 2. Introduction

최근 고해상도 합성에는 확률적인 likelihood를 기반으로 하는 모델들과 + autoregressive transformer의 수십억의 파라미터가 사용되고 있다.

GAN도 생성 분야에서 많이 쓰이고 있지만, 적대적 학습 특성상 복잡한 분포에 대해서는 적용하기 어렵다는 단점이 있다.

단계적인 denosing autoencoder를 적용한 diffusion 모델은 이미지 합성 분야에서 좋은 성과를 내며, class-conditional 이미지 합성과 super-resolution에서 SOTA를 달성했다.

하지만 이러한 Diffusion model들(DMs)은 훈련과 평가 시에 매우 큰 컴퓨팅 자원을 필요로한다. 따라서 DMs의 컴퓨팅 연산을 줄이며, 성능 저하없이, 성능을 높일 수 있는 방향으로 연구할 필요가 있다.

<br>

## 2.1. Departure to Latent Space

likelihood를 기반으로 하는 모델은 두 단계로 나눌 수 있다.

1. Perceptual compression
    - 인지적인 부분을 학습하여, 빈번하게 발생하는디테일을 제거하지만, 의미적인 변화를 학습하지 못한다. 예를 들어, 사진 속 사람이 누구인지는 알지만, 어떠한 디테일이 변화하는지는 알지 못한다.
2. Semantic compression
    - 데이터의 의미적인, 특징적인 부분을 학습한다.

따라서 인지적인 부분과 의미적인 부분을 모두 학습할 수 있는, 적합한 컴퓨팅 연산 공간을 찾고자 한다. 

아래와 같이 AutoEncoder의 잠재 공간으로부터 이미지를 효율적으로 생성할 수 있다: 

1. AutoEncoder를 통해 데이터 공간과 인지적으로 동일한 저차원 표현 공간을 얻을 수 있다.
2. DMs를 전체 이미지 픽셀이 아닌, 사전학습된 잠재 공간에서 학습한다. 그렇기에 공간 압축에 큰 신경을 쓰지 않아도 된다.

이러한 방식의 모델을 Latent Diffusion Models (LDMs)라고 정의하였다.

LDMs의 장점으로, AutoEncoder으로 한번 학습하고, 다양한 DMs 학습과 task에 재사용할 수 있다. 

따라서 기존의 큰 컴퓨팅 연산으로 여러 Diffusion 모델을 실행해보지 못했지만, 이 아키텍쳐를 기반으로 다양한 Diffusion 모델들을 돌려볼 수 있는 장점이 있다.

<br>

## 2.2. Contribution

- latent space를 통해 효율적으로 고차원의 데이터를 정확하고, 세부적으로 복원할 수 있으며, 고화질 합성도 가능하다.
- 컴퓨팅 연산 비용을 줄임과 동시에 다양한 Task(unconditional image synthesis, inpainting, stochastic super-resolution)에서 경쟁력 있는 성과를 달성했다.
- 초고해상도, 인페인팅, 시맨틱 합성 등 고도로 조건이 까다로운 작업의 경우 컨볼루션 방식으로 모델을 적용하여 1024x1024 픽셀의 크고 일관된 이미지를 만들 수 있다.
- cross-attention을 기반으로 하여 멀티모달(class-conditional, text-to-image, layout-to-image) 학습이 가능하도록 하였다.

<br>

# 3. Method

![Untitled](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/4523b80c-0b28-4a05-bed5-91c930779da9)

![Untitled 1](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/d5d6ba3e-4d6b-4251-b6cb-1d85cb27aca8)

- 목적: **생성과정을 학습하는 단계(Latent Diffusion Models)**와 **압축하는 것을 학습하는 단계(Perceptual Image Compression)**를 분리하기
- 방법: AutoEncoder 사용
    - 효과: perceptually equivalent한 공간을 학습하며, 컴퓨팅 연산 복잡도를 줄여준다.
- 장점:
    - sampling이 저해상도 공간에서 이뤄지기 때문에, 고해상도 이미지 생성시에도 효율적인 연산이 가능하다.
    - U-Net의 inductive bias를 사용하여 압축시에 손실을 줄여준다.

<br>

## 3.1. Perceptual Image Compression

Perceptual Image Compression 부분은 AutoEncoder로 구현되었다.

Auto Encoder를 학습시키기 위해서 combination of a perceptual loss와 patch-based adversarial objective를 사용하였다.

⇒ local realism을 가능하게 하고, bluriness를 줄여준다.

<br>

### 3.1.1. Loss

- Perceptual loss function + patch-based adversarial objective function  
    ![Untitled 2](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/cf4b25a1-fa43-4e1d-acd7-9f7063ff6bcf)  
    - Perceptual loss
        - Content Loss + Adversarial Loss로 구성되어있다.
            
            ![Untitled 3](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/882ac43b-d7ac-4142-96e7-08dd530d29c6)

            
        - Adversarial loss
            
            ![Untitled 4](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/c3e44042-d8d9-4f6c-ab56-ef6008127fcd)

            
            - Generator가 생성한 이미지를 진짜라고 판단할 확률을 최대화하는 방향으로 최적화할 수 있다.
        - Content loss
            
            ![Untitled 5](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/608b8a3e-74cf-45b1-baf8-6dafd46f34a9)

            
            - 가능한 고해상도 이미지가 여러 개 존재할 수 있으므로, 정답 이미지와 생성된 이미지를 pretrained CNN 모델에 통과시켜 얻어낸 feature map 사이의 유클리드 거리를 구하여 Loss 계산한다.  

<br>

- 효과
    - 이미지 부분들을 더욱 현실적으로 재구성하고, 흐릿하지 않도록 함으로써, 데이터 복원을 데이터의 특정공간(manifold)으로 제한될 수 있도록 한다.

<br>

- 수식  
    ![Untitled 2](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/cf4b25a1-fa43-4e1d-acd7-9f7063ff6bcf)    
    - $x ∈ R^{H×W×3}$ : RGB 입력 이미지
    - $\Epsilon$ : $x$를 인코딩하는 인코더
    - $z = \Epsilon(x)$: $x$가 인코더를 통과해서 나온 잠재변수
    - $D$ : 잠재변수에서 이미지를 복원하는 디코더
    - $\tilde{x} = D(z) = D(\Epsilon(x))$ : 복원된 이미지

<br>

- 잠재공간의 분산이 커지는 것을 막기 위해 2가지의 regularization을 진행했다.
    1. $KL-reg$
        - 학습된 latent 공간에 표준정규분포로 근사되는 방향으로 약간의 KL 패널티를 부여한다.
        - VAE와 유사
    2. $VQ-reg$
        - 디코더에서 벡터 양자화(Vector quantization) 층을 사용한다.
            - Vector quantization이란 연속적인 벡터들을 이산화하여, 인덱싱할 수 있도록 하는 것이다.

<br>

## 3.2. Latent Diffusion Models

![Untitled 6](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/a22e4a6b-08ac-49b3-910c-9191cf4d9162)

- $\epsilon_{θ}(z_t, t)$: denoising autoencoder: $z_t$로부터 노이즈가 제거된 값 $z_{t-1}$ 을 예측하도록 훈련된다.
    - U-Net으로 구현된다.

<br>

- **LDMs은 인코더와 디코더로 이루어진 사전학습된 perceptual compression model으로써, 빈도가 높고, 인지할 수 없는 세부사항들을 제거한 효율적인 저차원 공간을 얻을 수 있다.**
    
    픽셀 단위의 고차원 공간에 적용되는 이전의 DMs에 비해 저차원 공간의 LDMs에서는 중요하고 의미적인 데이터에 집중할 수 있기 때문에 확률 기반 생성 모델에 더욱 적합하다고 할 수 있다. 또한 컴퓨터 연산적으로 비용이 더 적기에 효율적이라고 할 수 있다.
    

- LDMs은 Autoregressive, attention기반의 트렌스포머를 사용한 이전 모델들과는 다르게, 이 논문에서는 image-specific inductive bias를 사용하였다.

- auto-regressive, attention 기반의 트랜스포머 모델과 image specific inductive bias의 특징과 차이점이 뭘까? (**feat. chatgpt**)
    - **Auto-regressive 모델**
        - Auto-regressive 모델은 시퀀스 데이터를 생성하거나 
        예측할 때 이전 시점의 출력을 현재 시점의 입력으로 사용하는 모델입니다. 이전에 생성된 내용에 기반하여 다음 단계를 예측하므로 
        생성 과정이 순차적으로 진행됩니다. GPT(Generative Pre-trained Transformer)는 이러한 
        auto-regressive 모델의 예입니다.
    - Attention 기반의 트랜스포머 모델
        - Attention 메커니즘은 입력 시퀀스의 각 요소가 **출력에 영향을 주는 정도를 가중치로 표현**하는 기술입니다. 이를 통해 **입력 시퀀스의 중요한 부분에 모델의 주목을 확대**시킬 수 있습니다. 트랜스포머 모델은 이러한 attention 메커니즘을 활용하여 입력의 전체 컨텍스트를 잘 캡처하여 자연어 처리 작업을 수행합니다.
    - Image Specific Inductive Bias
        - **Inductive Bias**: Inductive bias는 머신 러닝 모델이 학습 과정에서 어떤 가정을 기반으로 일반화를 수행하는지를 결정하는 요소입니다. 이미지 특정 귀납적 편향은 이미지 처리 작업에 특화된 inductive bias를 나타냅니다.
        - **Image-Specific Inductive Bias**: 이미지 처리 작업에서는 픽셀 간의 공간 구조 및 특정 패턴이 중요하므로 모델은 이러한 이미지 특징을 고려하여 학습되어야 합니다. 예를 들어, 합성곱 신경망(Convolutional Neural Network, CNN)은 이미지 처리 작업에서 특별히 효과적이며, 이는 이미지의 공간적 구조를 고려한 inductive bias를 가지기 때문입니다.
    - **특징 및 차이점:**
        - Auto-regressive 및 attention 기반의 트랜스포머 모델은 주로 자연어 처리 작업에 활용되며, 문장의 구조 및 의미를 모델링합니다.
        - 이미지 특정 귀납적 편향은 이미지 처리 작업에서 중요한 공간적 구조와 패턴을 모델링합니다.
        - Auto-regressive 및 attention 기반의 트랜스포머 모델은 시퀀스 데이터를 다루는 데 강점을 가지고 있습니다.
        - 이미지 특정 귀납적 편향은 이미지 처리 작업에 더 적합한 모델 구조 및 학습 방법을 채택하여 이미지 특징을 잘 추출할 수 있도록 도와줍니다.
    
<br>

## 3.3. Conditioning Mechanisms

![Untitled 7](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/e91665fa-d720-416c-8b5d-40872666a9e8)  
![Untitled 8](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/672f8135-6122-4d17-ace9-9a4b8247a1dc)  
- 이미지-조건 쌍을 기반으로 위의 $L_{LDM}$을 통해 conditional LDM를 학습한다.
- $\epsilon_{θ}(z_t, t, y)$: conditional denoising autoencoder: 조건 y와 $z_t$로부터 노이즈가 제거된 값 $z_{t-1}$ 을 예측하도록 훈련된다.
- **cross-attention 기법으로 U-Net 백본을 강화시켰다.** cross-attention은 다양한 input modality의 attention 기반 모델을 학습하기에 효과적이다.

<br>

- 조건 y를 모델에 투입하기 전에, 전처리하기 위해서 **domain specific encoder $\tau_\theta$를 사용**한다.
    - encoder $\tau_\theta$는 y를 중간단계의 representation으로 만들고, cross-attention을 통해 U-Net의 중간 단계의 layer로 매핑된다.
        
        ![Untitled 9](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/1ec9e478-5a11-4ab3-8302-eb5d3907b746)

        
        - $Attention(Q, K, V)$: cross-attention layer
        - $ϕ_i(z_t) ∈ R^{N×d^i_\epsilon}$:  $\epsilon_\theta$ 를 실행한 U-Net의 중간단계 representation
        - $W_V^{(i)}∈R^{d×d^i_\epsilon}, W_Q^{(i)}∈R^{d×d_\tau}, W_K^{(i)}∈R^{d×d_\tau}$: 학습 가능한 projection matics
        - $\tau_\theta(y)$: 조건 y를 인코딩한 결과(representation)
    - $\tau_\theta$와 $\epsilon_\theta$는 $L_{LDM}$을 통해 함께 최적화된다.

<br>

# 4. Experiments

- pixel-based diffusion model과 LDMs와 비교
    - VQ-regularized latent spaces에서 학습된 LDMs 모델이 가끔(sometimes) 더 좋은 sample quality를 냈다.
    

## 4.1. On Perceptual Compression Trade-offs

- downsampling factor를 다르게 하여 실험 진행  
    ![Untitled 10](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/fc006eda-72eb-4135-beb9-f6cf9cdeced9)  
    - LDM-f ⇒ f ∈ {1, 2, 4, 8, 16, 32}
        - LDM-1 (= pixel-based DMs)
        - ⇒ LDM-{4-16}이 되어도 좋은 성능을 내며, 금방 수렴한다는 걸 알 수 있다.

<br>

- Denoising step을 다르게 하여 sampling 속도 비교 실험 진행 (with the DDIM sampler)  
    ![Untitled 11](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/ead66061-4875-439b-97f1-8a70d482783c)  
    - LDM-{4-8}이 좋은 성능을 낸다는 것을 알 수 있다.

<br>

# 5. Limitations

- 여전히 GANs보다는 느리다.
- 섬세한 픽셀 공간에서 높은 정확도를 필요로 할 때는, 정확도의 한계가 있다.