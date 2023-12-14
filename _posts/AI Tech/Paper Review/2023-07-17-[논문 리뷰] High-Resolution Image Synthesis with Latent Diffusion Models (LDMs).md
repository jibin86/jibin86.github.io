---
title:  "[논문 리뷰] LDMs: High-Resolution Image Synthesis with Latent Diffusion Models"
categories: [AI Tech, Computer Vision, Paper Review]
tags: [diffusion]
typora-root-url: ../
---

[https://arxiv.org/pdf/2112.10752.pdf](https://arxiv.org/pdf/2112.10752.pdf)

이 논문은 2022년도 CVPR에 등재된 논문이다. Diffusion은 컴퓨팅 연산이 많이 필요하며, 단순히 원본 이미지를 복원하는 것보다, Text를 통해서 어떻게 이미지를 생성, 수정할 수 있는지, 원리가 궁금하였기에 이 논문을 찾아 읽게 되었다.  

이 논문은 직접적인 Editing 과정을 설명하진 않지만, 매우 큰 컴퓨팅 연산을 필요로하는 기존의 diffusion 모델을 개선하고, text를 기반으로 이미지를 생성한다는 점에서 공부할 부분이 많고, 흥미로웠기에 이 논문을 선정하여 읽게 되었다.

Stable Diffusion에 들어가기 앞서 사전지식을 정리해보자.

# Background

## ❓ Latent Space란?

autoencoder 같은 모델에서 **고차원의 입력데이터를 저차원으로 투영**하는데 이 과정에서 모델은 데이터의 **중요한 특성**을 뽑아내고, 이 특성들의 **분포 공간을 latent space**라고 한다.



## ❓ Autoencoder란?

이미지를 latent space으로 차원 축소할때 Autoencoder기법을 사용한다. 인코더 디코더로 이루어져 있으며 **인코더에서는 이미지를 낮은 차원(latent space)으로 압축**하며 **디코더는 latent space로부터 이미지를 복원**하는 역할을 한다.

![Untitled](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/Untitled.png)

- VAE

  - 이미지를 복원하는 모델 중 하나이다.

  ![Untitled](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/Untitled 1.png)

## ❓cross-attention이란?


![Untitled](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/Untitled 2.png)

**self attention**은 한 시퀀스 내부에서 정보를 교환하는 방법이며 입력 시퀀스의 각 위치에서 다른 위치들의 정보와의 관계를 참조한다.

하지만 **cross attention**은 왼쪽 그림과 같이 두개의 서로 다른 시퀀스 간에 정보를 교환한다. **cross attention**을 통해 서로 다른 시퀀스(모달)을 이해할 수 있다.



## Diffusion model

- 이미지를 확률적으로  복원하는 모델 중 하나이다.

  - 이미지에 가우시안 노이즈를 섞어 빽빽한 노이즈로 만든 다음에, 그 노이즈를 다시 제거하며 원본 이미지로 복원해나가는 모델이다.

    ![Untitled](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/Untitled 3.png)

![Untitled](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/Untitled 4.png)

![K-062.jpg](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/K-062.jpg)

- VAE와 다르게 encoder에서 decoder까지 이르는 과정을 diffusion model은 step을 나눠서 진행하게 된다.

  ![Untitled](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/Untitled 1-1702524721249-164.png)

## S**table diffusion (Latent Diffusion Model)**

💡 stable diffusion은 Autoencoder와 diffusion model을 섞었다고 볼 수 있음!
![K-063.jpg](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/K-063.jpg)

1. input값을 encoder에 넣고 latent variable을 만들어 낸다.
2. latent space상에서 forward process를 통해 noise를 주입하고
3. 다시 노이즈를 단계적으로 제거하는 과정을 거친다.
4. 마지막으로 decoder로 latent variable를 풀어서 원본 이미지 크기로 만들어낸다.

<br>

# Abstract

**저자가 무엇을 해내고 싶어했는가?**

1. 매우 큰 컴퓨팅 연산을 필요로하는 기존의 diffusion 모델을 개선하기

   기존 Diffusion model ⇒ 픽셀 단위로 가우시안 분포를 학습

**이 연구의 접근에서 중요한 요소는 무엇인가?**

1. AutoEncoder를 적용 ⇒ 적은 연산으로 가우시간 분포 학습 가능!
2. cross-attention layer를 도입 ⇒ 멀티모달 학습 가능

------

**배경**

기존의 Diffusion 모델은 기존의 데이터의 크기를 그대로 유지하며, 픽셀 단위로 가우시안 분포를 학습하는 모델이다. 따라서 복원되는 이미지의 퀄리티는 좋지만 픽셀 단위로 연산을 하기 때문에 매우 큰 컴퓨팅 연산이 필요하다.

**방법1**

이런 단점을 보완하기 위해, 제한된 컴퓨팅 자원으로 Diffusion 모델의 퀄리티와 유연성을 유지하도록하는 사전학습된 AutoEncoder를 적용하였다.

**방법2**

또한 cross-attention layer를 도입하여 text, bounding box, 고해상도 합성 등과 같은 조건적인 입력에 대해서도 생성할 수 있도록 모델을 업그레이드하였다.

**결과**

이 모델은 다양한 task에 대해서 이미지 inpainting, class-conditional image synthesis, unconditional 이미지 생성, super-resolution 등  SOTA를 달성하였다.



# Method

## 1. Departure to Latent Space

확률(likelihood)을 기반으로 하는 모델은 두 단계로 나눌 수 있다.

1. Perceptual compression
   - 인지적인 부분을 학습하여, 빈번하게 발생하는 디테일을 저차원으로 압축하는 단계
2. Semantic compression
   - 데이터의 의미적인, 특징적인 부분을 학습하는 단계

![Untitled](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/Untitled 5-1702524755654-167.png)

AutoEncoder를 통해 원본 데이터 공간과 인지적으로 동일한 저차원 표현 공간을 얻을 수 있다.

⇒ Diffusion model을 전체 이미지 픽셀이 아닌, 사전학습된 잠재 공간에서 학습할 수 있다.

⇒ 따라서 AutoEncoder의 잠재 공간으로부터 이미지를 효율적으로 생성할 수 있게 된다.

이러한 방식의 모델을 **Latent Diffusion Models (LDMs)**라고 정의하였다.

LDMs의 장점으로, AutoEncoder으로 한번 학습하고, 다양한 DMs 학습과 task에 재사용할 수 있다.

따라서 기존의 큰 컴퓨팅 연산으로 여러 Diffusion 모델을 실행해보지 못했지만, 이 아키텍쳐를 기반으로 다양한 Diffusion 모델들을 돌려볼 수 있는 장점이 있다.



## 2. Perceptual Image Compression (Autoencoder)

Perceptual Image Compression 부분은 AutoEncoder로 구현되었다.

### Loss

- Perceptual loss

  - Content Loss + Adversarial Loss로 구성되어있다.

    ![Untitled](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/Untitled 6-1702524761223-169.png)

  - **Adversarial loss**

    ![Untitled](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/Untitled 7.png)

    - Generator가 생성한 이미지를 진짜라고 판단할 확률을 최대화하는 방향으로 최적화할 수 있다.

  - **Content loss**

    ![Untitled](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/Untitled 8.png)

    - 가능한 고해상도 이미지가 여러 개 존재할 수 있으므로, 정답 이미지와 생성된 이미지를 pretrained CNN 모델에 통과시켜 얻어낸 feature map 사이의 유클리드 거리를 구하여 Loss 계산한다.



## 3. Latent Diffusion Models

### Loss

- Diffusion Model

  ![Untitled](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/Untitled 9.png)

- Latent Diffusion Model

  ![Untitled](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/Untitled 10.png)

- $\epsilon_{θ}(z_t, t)$: denoising autoencoder: $z_t$로부터 노이즈가 제거된 값 $z_{t-1}$ 을 예측하도록 훈련된다.

  - U-Net으로 구현된다.



## 4. Conditioning Mechanisms

![Untitled](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/Untitled 11.png)

- 이미지-조건 쌍을 기반으로 위의 $L_{LDM}$을 통해 conditional LDM를 학습한다.

- $\epsilon_{θ}(z_t, t, y)$: conditional denoising autoencoder: 조건 y와 $z_t$로부터 노이즈가 제거된 값 $z_{t-1}$ 을 예측하도록 훈련된다.

- **cross-attention 기법으로 U-Net 백본을 강화시켰다.** cross-attention은 다양한 input modality의 attention 기반 모델을 학습하기에 효과적이다.

- 조건 y를 모델에 투입하기 전에, 전처리하기 위해서 

  domain specific encoder $\tau_\theta$를 사용

  한다.

  - encoder $\tau_\theta$는 y를 중간단계의 representation으로 만들고, cross-attention을 통해 U-Net의 중간 단계의 layer로 매핑된다.

    ![Untitled](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/Untitled 12.png)

    - $Attention(Q, K, V)$: cross-attention layer
    - $ϕ_i(z_t) ∈ R^{N×d^i_\epsilon}$:  $\epsilon_\theta$ 를 실행한 U-Net의 중간단계 representation
    - $W_V^{(i)}∈R^{d×d^i_\epsilon}, W_Q^{(i)}∈R^{d×d_\tau}, W_K^{(i)}∈R^{d×d_\tau}$: 학습 가능한 projection matics
    - $\tau_\theta(y)$: 조건 y를 인코딩한 결과(representation)

- $\tau_\theta$와 $\epsilon_\theta$는 $L_{LDM}$을 통해 함께 최적화된다.



# Experiments

1. Audoencoder의 downsampling factor를 다르게 하여 실험 진행한다.

   ![Untitled](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/Untitled 13.png)

   - LDM-f ⇒ f ∈ {1, 2, 4, 8, 16, 32}
     - LDM-1 (= pixel-based DMs)
     - ⇒ LDM-{4-16}이 되어도 좋은 성능을 내며, 금방 수렴한다는 걸 알 수 있다.

2. Denoising step을 다르게 하여 sampling 속도 비교 실험 진행 (with the DDIM sampler)

   ![Untitled](/../../images/2023-07-17-[논문 리뷰] High-Resolution Image Synthesis with Latent Diffusion Models (LDMs)/Untitled 14.png)

   - LDM-{4-8}이 좋은 성능을 낸다는 것을 알 수 있다.



# 5. Limitations

- 여전히 GANs보다는 느리다.
- 섬세한 픽셀 공간에서 높은 정확도를 필요로 할 때는, 정확도의 한계가 있다.