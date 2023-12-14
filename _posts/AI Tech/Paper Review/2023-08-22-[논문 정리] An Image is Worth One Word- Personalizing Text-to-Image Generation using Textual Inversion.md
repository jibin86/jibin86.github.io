---
title:  "[논문 정리] Text Inversion: An Image is Worth One Word: Personalizing Text-to-Image Generation using Textual Inversion"
categories: [AI Tech, Computer Vision, Paper Review]
tags: [diffusion]
typora-root-url: ../
---

![Untitled](https://i.ibb.co/m6SMh62/Untitled.png)

이번 포스트에는 Text-to-Image 생성 모델 중 하나인 Text Inversion 모델을 다뤄보도록 할 것이다. 
Text Inversion은 기존에 학습된 데이터만 사용하여 이미지를 생성하는 것이 아닌, 커스터마이즈된 이미지를 생성하는 모델이다.

# 1. Abstract

- **기존 text-to-image 모델의 한계**
    - 사용자가 제공한 고양이 사진을 그림으로 바꾸거나 좋아하는 장난감을 바탕으로 새로운 이미지를 생성하는 것이 어려웠다.
- **논문에서 제시하고자하는 방법**
    - 사용자가 제공한 3~5개의 이미지만을 사용하여 frozen text-to-image model의 임베딩 공간에 새로운 “words”를 통해 제공된 이미지의 concept을 표현하는 방법을 학습한다.
    - “words”: 자연어로 구성되어, 내가 원하는 개인화된 creation을 직관적으로 나타낸다.
        - 한 word의 임베딩만으로도 독특하고 다양한 concept을 담아낼 수 있다.

<br>

# 2. Introduction

## 2.1. Problem

Text Inversion 모델을 사용하여 다음과 같은 문제를 해결하고자 하였다.

1. **기존 Text description의 한계**
    - Text description은 사용자의 원하는 대상을 사용하는 사용자의 능력에 따라 성능이 제한된다. 또한 Text로는 대상을 충분히 묘사하기 어렵다.
    - 따라서 Text description으로만 사용자의 대상을 설명하기에는 한계가 있다.
2. **Large scale model에 새로운 개념을 도입할 때의 문제:**
    1. 새로운 개념마다 확장된 데이터 세트로 모델을 다시 학습하는 데는 매우 큰 비용이 든다.
    2. 몇 가지 이미지로 fine-tuning 하면, 이전에 학습된 체계를 무너뜨리는 위험이 존재한다.
        - 사전에 학습된 prior knowledge를 잊어버리기 쉽고, 새로 학습된 개념과 이전의 prior knowledge를 동시에 접근하기 어렵다는 문제가 있다.  
- 기존 방식의 문제:
    - 따라서 위와 같은 방식으로는 Concept의 형태를 정확하게 포착하기 어렵고, 상당한 시각적 손상(visual corruption)이 발생할 수 있다.

<br>

## 2.2. Solution

- pre-trained text-to-image models의 텍스트 임베딩 공간에서 새로운 “words”를 찾도록한다.
  
    ![Untitled](https://i.ibb.co/yhBxQkC/Untitled-1.png)
    
- **STEP**
    1. S*을 tokens으로 변환한다.
    2. 이 tokens는 continous vector representations (embeddings, $v$)으로 변환된다.
    3. embedding vectors는 a single conditioning code $c_θ(y)$로 변환된다.
        - a single conditioning code $c_θ(y)$
            - 생성 모델을 가이드하는 역할을 한다.
    4. reconstruction objective를 사용하여 embedding vector v∗를 pseudo-word S*에 맞도록 최적화시킨다.
       
        ![Untitled](https://i.ibb.co/jVttXx7/Untitled-2.png){: width="500"}
        
    
- **텍스트 인코딩 process**
    - Goal: to **find new embedding vectors that represent new, specific concepts.**
    - 방법: 입력 문자열을 a set of token로 변환시키기
    
    <br>

- **Pseudo-word란**
    - 새로운 임베딩 벡터를 새로운 **Pseudo-word**로 표현하며, 이를 S∗로 표시한다.
    - pseudo word는 문장을 만들 때, 평범한 문자처럼 조합된다.
        - examples
            - “a photograph of $S_∗$ on the beach”
            - “an oil painting of a $S_∗$ hanging on the wall”
            - “a drawing of $S_*^1$ in the style of $S_*^2$”.
    - 위와 같이 Pseudo-word를 사용하여 생성모델을 건드리지 않고 새로운 개념을 표현할 수 있다.

    <br>

- **pseudo-word를 찾는 방법**
    - 필요한 것
        1. a fixed, pre-trained text-to-image model
        2. a small (3-5) image set depicting the concept
    - 목표
        1. “A photo of S∗ 이라는 형식의 문장이 3~5개의 image set만으로 이미지를 재구성할 수 있도록 **하나의 word embedding**을 찾는다.
        2. Text Encoder의 임베딩 공간에서 high-level 의미 정보와 세밀한 시각적 디테일을 모두 표현할 수 있는 새로운 **pseudo-word**를 찾는다.
            - 그 embedding은 optimization process를 통해 얻을 수 있다.
                - optimization process ⇒ Textual Inversion이라고 부른다.

<br>

# 3. Method

- **과정 요약:**
    - discrete input text를 continuous vector representation으로 변환하는데, 이 representation이 text를 잘 표현하는 방향으로 최적화된다.

## 3.1. Latent Diffusion Models

- 논문에서 제시한 방식을 LDMs 모델에 적용해보았다.
- LDMs은 AutoEncoder와 Diffusion 모델로 구성되었다.
    1. AutoEncoder
        - 대규모의 이미지로 사전학습되어있다.
        - 인코더 부분에서는 image x를 잠재공간 z으로 변환한다.
        - KL-Divergence loss 또는 Vector quantization을 통해 정규화된다.
        - 디코더 부분에서는 잠재공간 z를 이미지로 다시 변환한다.
    2. Diffusion model
        - 사전학습된 잠재공간 z를 입력 받는다.
        - 이런 diffusion 모델에 class label, segmentation mask, text-embedding 등 조건을 추가할 수 있다.
        - $c_θ(y)$를 y라는 조건을 부여하는 모델이라고 하자. 다음과 같이 Loss에 대한 식을 세울 수 있다.            
            ![Untitled](https://i.ibb.co/sHyZf5d/Untitled-3.png)            
            - t: time step
            - $z_t$: time step $t$에 대한 노이즈가 추가된 잠재 변수
            - $\epsilon_\theta$: denoising network
            - Training 시에, $\epsilon_\theta$와 $c_θ$는 LDM loss를 최소화하는 방향으로 동시에 최적화된다.
            - Inference 시에, 잠재 공간에서 샘플링 되고, 반복적으로 노이즈 제거하는 과정을 거쳐, 새로운 이미지 잠재변수 $z_0$를 생성한다.
            - 이 잠재변수 $z_0$는 사전학습된 AutoEncoder의 디코더를 거쳐 이미지로 변환된다.
- 이 논문에서는 Diffusion model로 1.4 billion parameter text-to-image model을 사용하였는데, 이는 LAION-400M dataset에서 훈련된 모델이다.
    - 여기서 $c_θ(y)$는 BERT text encoder로 구현되었고, y는 text prompt이다.

<br>

## 3.2. Text embeddings.

![Untitled](https://i.ibb.co/yhBxQkC/Untitled-1.png)  
- 위에서 언급했다시피, Text Encoder 모델로 BERT를 사용하였다.
- **일반적인 Text Embedding 과정**
    1. 입력 문장의 각 word 또는 sub-word를 token으로 변환한다.
        - token: pre-defined dictionary의 인덱스이다.
    2. 각 토큰을 임베딩 벡터로 변환한다.
        - 각 토큰은 고유한 embedding vector와 연결되는데, embedding vector는 인덱스 기반의 lookup을 통해 얻을 수 있다.
        - 여기서 임베딩 벡터는 text encoder $c_\theta$를 통해서 학습된다.
- 논문에서는 임베딩 과정에서 기존 벡터를 새로운 개념 $S_\*$에 대해 학습된 벡터 $v_\*$로 대체했다. 즉 새로운 개념을 vocabulary에 추가했다는 의미이다.

## 3.3. Textual inversion.

- 그래서  새로운 개념 $S_\*$에 대해 학습된 벡터 $v_\*$를 얻기 위해서, 3~5개 정도의 다양한 background, pose에 대한 target 이미지로 fine-tuning 한다.
    - 위에서 언급한 LDMs Loss를 최소화하는 방향으로 학습하여 최적의 $v_*$를 찾는다.          
        ![Untitled](https://i.ibb.co/M66Qbv7/Untitled-4.png)
        
        - 위 Loss는 Reconstruction Loss라고 부르며,  $c_\theta$와 $\epsilon_\theta$는 fixed 되었다.              
            - 생성에 조건을 부여하기 위해, CLIP ImageNet templates에서 neutral context texts를 랜덤하게 뽑았다.
                - ex) “A photo of S∗”, “A rendition of S∗”, etc.

<br>

## 3.4. Implementation details

![Untitled](https://i.ibb.co/m6SMh62/Untitled.png)

<br>

# 4. Qualitative comparisons and applications

## 4.1 Image variations

![Untitled](https://i.ibb.co/MGgvY3S/Untitled-5.png)

## 4.2 Text-guided synthesis

![Untitled](https://i.ibb.co/J3Gzmcm/Untitled-6.png)

![Untitled](https://i.ibb.co/b5k3cxG/Untitled-7.png)

## 4.3 Style transfer

![Untitled](https://i.ibb.co/YZGvFdB/Untitled-8.png)

## 4.4 Concept compositions

![Untitled](https://i.ibb.co/d0pNTCr/Untitled-9.png)

## 4.5 Bias reduction

![Untitled](https://i.ibb.co/xhV61WY/Untitled-10.png)

## 4.6 Downstream applications

![Untitled](https://i.ibb.co/3CX6vjr/Untitled-11.png)

<br>

# 5. Limitation

- diffusion 모델을 학습하지 않기 때문에, diffusion 모델의 학습과정에 없는 데이터(ex_ 내 얼굴)가 주어진다면, 제대로 customize되기 어렵다.