---
title:  "[논문 정리] CoDeF: Content Deformation Fields for Temporally Consistent Video Processing"
categories: [AI Tech, Computer Vision, Paper Review]
tags: [diffusion]
---

[https://arxiv.org/pdf/2308.07926.pdf](https://arxiv.org/pdf/2308.07926.pdf)

오늘은 CoDeF 논문을 읽어보고자 한다.  
이 논문도 이전 논문과 마찬가지로 텍스트 기반으로 비디오를 수정하는 분야를 다룬 논문이다.  
논문의 목적과, 모델 구조를 중심으로 정리하였다.  

- 저자가 무엇을 해내고 싶어했는가?
    - 기존 Video Processing의 문제인 **high temporal consistency를 유지하지 못하는 문제를 해결하고자 한다.**
    - 즉, temporal consistency를 유지할 수 있는 비디오를 만들고자 한다.
- 이 연구의 접근에서 중요한 요소는 무엇인가?
    - 비디오를 대표하는 하나의 이미지(**Canonical content field)**와, 모션 정보(**Temporal deformation field)**만을 가지고, 비디오를 편집하는 새로운 방식을 제시한다.

<br>

# 1. Abstract

![Untitled](https://i.ibb.co/mSF7z1R/Untitled.png)

- 이 논문에서는 **Content Deformation field**를 제안한다.
    - Content deformation field는 아래 두 부분으로 구성되었다.
        1. **Canonical content field** $C$
            - 비디오의 전체적인 정적인 컨텐츠, 즉 비디오를 대표하는 이미지(canonical image)를 의미한다.
        2. **Temporal deformation field** $D$
            - 비디오 각 프레임의 변형되는 부분을 기록한다.

<br>

- 이 논문에서는 canonical content field가 비디오의 의미적인 정보(object shape 등)를 제대로 반영할 수 있도록 optimization 과정에서 몇 가지 규제들을 도입하였다. ⇒ 4.3. Model Design  

<br>

- **content deformation field에서는** 다음과 같은 과정을 거친다.
    1. Temporal deformation field를 사용하여 Canonical image와 각 개별 프레임 간의 변형을 기록한다.
    2. 하나의 Canonical image에 image algorithm을 적용하여 이미지 처리를 수행한다.
    3. 기록된 변형 정보를 이용하여 canonical image에서 처리된 결과를 전체 비디오의 모든 프레임에 적용하여 전체 비디오를 변경시킨다.

<br>

- 결과
    - **Image-to-Image Translation ⇒ Video-to-Video Translation으로 확장 가능**
    - **Keypoint Detection ⇒ Keypoint Tracking으로 확장 가능**
        - Keypoint **감지**를 Keypoint **추적**으로 확장할 수 있다. 즉, 특정 지점이나 특징점을 감지하는 것을 넘어서 그것을 시간에 따라 추적할 수 있다.

<br>

# 2. Introduction

- 기존 Video Processing의 문제
    - **high temporal consistency를 유지하는 것이 어렵다.**
- 원인
    - 신경망 특유의 Randomness
    - 낮은 퀄리티의 비디오 데이터셋
    - 더 큰 컴퓨팅 리소스 필요함  
- 논문에서 제안: 
    - 비디오에 이미지 알고리즘을 적용하면서, temporal consistency를 유지할 수 있는 비디오를 만들고자 한다.   
    - ⇒ **2D hash-based image field + 3D hash-based temporal deformation field 방식 제안**  
        - **2D hash-based image field**
            - 이미지에 대한 정보를 해시 함수를 사용하여 표현하는 것이다.
                - 해시 함수: 임의의 데이터를 고정된 길이의 일련의 숫자로 변환하는 함수 (인코딩과 유사한 의미)
                - 효과: 이미지의 특정 특징을 나타내는 정보를 추출하거나, 비교할 수 있다.  
        - **3D hash-based temporal deformation field**
            - 비디오 내의 각 프레임을 해시 기반으로 표현하는 것이다.
                - 효과: 시간적인 변화를 해시 정보로 나타내어 비디오의 흐름이나 움직임을 다룰 수 있다.
            - **incorporation of multi-resolution** hash encoding 기법을 사용한다.
                - **multi-resolution**: 하나의 프레임을 원본 해상도와 줄인 해상도 두 가지로 동시에 표현하는 것
                - **Hash Encoding** (해시 인코딩): 데이터를 고정된 길이의 해시로 변환하는 것
                - 즉, temporal deformation에 대한 데이터(x, y, t)를 다중 해상도로 표현하고, 
                  표현된 각 해상도에 해시 인코딩을 적용하여 비디오의 움직임과 변형을 표현할 수 있도록 하였다.
                  ⇒ 물, 기체와 같은 non-rigid 물체들의 변형도 감지할 수도 있다.                      
                    ![Untitled](https://i.ibb.co/JvWWKD4/Untitled-1.png)

        <br>
        
    - deformation field의 능력이 너무 올라가면, 자연스러운 canonical image를 예측하기 어려울 수 있다.
      따라서 Training 하는 동안 annealed hash을 적용한다.
        - ⇒ coarse-to-fine training 기법: 처음에는 smooth deformation grid로 모든 rigid motions을 식별하고, 이후에 세부 디테일을 점진적으로 추가한다.
        - 이렇게 하므로써 canonical image의 자연스러움은 유지하고, 재구성(reconstruction)의 정확도(faithfulness)도 유지할 수 있다.

<br>

- 결과
    - temporal consistency와 texture quality에 대해 좋은 성능을 보여주며, zero-shot
    video translations with generative models 분야에서 SOTA를 달성했다.

<br>

- 과정을 한마디로 정리하자면,
1. 비디오에서 대표 이미지(canonical image)를 추출하고, 
2. 추출한 대표 이미지에 스타일을 바꾸던지 하고 싶은 처리를 하고, 
3. 이전에 비디오에서 측정한 temporal deformation(움직임 정보)을 기반으로 처리된 이미지 결과를 전파하여 비디오를 만든다.

<br>

# 3. Related Work

## 3.1. Implicit Neural Representation

- 좌표를 implicit representation으로 표현하기 위해서 기존에 Fourier positional encoding 방식을 써왔다.
- 하지만 훈련 속도를 높이기 위해서 기존의 Fourier positional encoding 방식에서, multi-resolution feature, grid or hash table와 같은 discrete representation 방식을 사용한다.
- 추가적으로, implicit deformation field는 다이나믹한 장면들을 잘 나타내준다는 연구 결과들이 많이 나왔다.
- 따라서 위 논문에서는 semantic 정보를 담은 canonical image와 deformation field를 사용하여 비디오를 재구성(reconstruction)하고자 한다.

<br>

## 3.2. Consistent Video Editing.

- Consistent Video Editing에는 주로 2가지의 접근 방식이 있다.
    1. propagation-based methods
        - 하나의 프레임을 수정하고, 수정된 하나의 프레임을 기반으로 전체 비디오를 수정하는 방식
        - 장점:연산 비용이 적고, 단순하다.
        - 단점: 복잡한 모션에는 부정확, 불일치가 나타날 수 있다.
        - 관련 논문:
            - [13] Space-time correspondence as a contrastive random walk. 2020
            - [14] Video propagation networks. 2017
            - [15] Stylizing video by example. 2019
            - [43] Artistic style transfer for videos. 2016
            - [53] Interactive video stylization using few-shot patch-based training. 2020
            - [60] Learning correspondence from the cycle-consistency of time. 2019

        <br>

    2. layered representation-based techniques
        - 비디오를 여러 레이어로 분해하여, 비디오를 수정하는 방식
        - 관련 논문:
            - [16] Layered neural atlases for consistent video editing. 2021
            - [23] Layered neural rendering for retiming people in video. 2020
            - [24] Associating objects and their effects in video through coordination games. 2022
            - [40] Unwrap mosaics: A new representation for video editing. 2008
            - [47] Layered depth images. 1998
            - [1] Text2live: Text-driven layered image and video editing. 2022
                - 최적화된 atlas를 수정하여, 시간적으로 일관적인 비디오를 만들 수 있다.

<br>

- 위 논문에서는 최적화된 비디오 representation을 사용하는 것은 맞지만, hash-based deformable design을 통합하여 semantic 정보를 담고 있는 canonical representation에 최적화에 더욱 초점을 맞출 것이다.
    - layered representation-based techniques과 CoDeF 비교          
        ![Untitled](https://i.ibb.co/dLRgHfv/Untitled-2.png)
        

<br>

## 3.3. Video Processing via Generative Models.

- 기존 모델들의 한계
    1. DDIM의 잠재공간과 cross-attention map을 통해 일관적인 생성하였지만, 여전히 생성의 **랜덤적인 특성** 때문에 시간적 일관성을 달성하기 어렵다는 단점이 있었다.
        - 관련 논문:
            - Tune-A-Video [64],
            - Text2Video-Zero [17],
            - FateZero [36],
            - Vid2VidZero [59],
            - Video-P2P [22]

        <br>

    2. 입력 텍스트와 의미적으로 일치하게 비디오를 생성할 수 있지만, **상당한 컴퓨터 연산**이 필요하다는 점에서 제한적이다.
        - 관련 논문:
            - NUWA [63], 
            - CogVideo [12], 
            - Phenaki [55], 
            - Make-A-Video [48], 
            - Imagen Video [10], 
            - Gen-1 [7]

<br>

# 4. Method

## 4.1. Problem Formulation.

- a flattened **canonical image $I_c$**와 **deformation field $D$**를 사용해서 비디오 $V$를 만든다.
    - $V$: video
    - $I_c$: a flattened canonical image
    - $D$: deformation field
    - $X$: canonical image을 처리하는 알고리즘

<br>

- 비디오 Representation은 다음과 같은 특징을 갖는다.
    1. Fitting Capability for Faithful Video Reconstruction
        - The representation should possess the ability to accurately fit large rigid or non-rigid deformations in videos.
    2. Semantic Correctness of the Canonical Image.
    3. Smoothness of the Deformation Field
        - that guarantees temporal consistency and correct
        propagation

<br>

## 4.2. Content Deformation Fields

- Content Deformation Fields는 **canonical field와 deformation field로 구성되었다.**
    - canonical field $C$와 deformation field $D$는 각각 2D, 3D hash table로 구현되며, 
    hash table은  coordinated-based MLP로 표현할 수 있다.
    
    ![Untitled](https://i.ibb.co/XJC9CDW/Untitled-3.png)
    

<br>

### 4.2.1. 2D Hash Encoding for Canonical Field.

- canonical field $C$ 는 continuous representation으로 모든 flattened textures를 둘러싼다.
    - $C: γ_{2D}(x) = (x, F_1(x),\space \dots, F_L(x))$:
        - ⇒ 2D 좌표를 Feature 벡터로 변환시킨다.
        - 세밀한 정보도 캐치할 수 있도록 multi-resolution hash encoding 적용한다.
            - $γ_{2D} : R^2 → R^{2+F×L}$ ⇒ 2(=x,y좌표) x 3(=RGB) x L(=resolution수)
                - L ⇒ multi-resolution의 resolution 수
                - F ⇒ 각 레이어의 Feature 차원 수
            
            <br>

        - $F_l(x)$ : x → c
            - x를 $l$ 번째 resolution으로 표현한 features다.
              
                ![Untitled](https://i.ibb.co/9hnyctD/Untitled-4.png)
                
                1. x ⇒ 2D position (x, y 좌표)
                2. c ⇒ r, g, b color

<br>

### 4.2.2. 3D Hash Encoding for Deformation Field.

![Untitled](https://i.ibb.co/98LGMvm/Untitled-5.png)

- deformation field에서는 관측된 위치와 대표 이미지(canonical image) 위치를 연결한다.
- deformation field는 3D hash table로 표현되며, 이는 MLP로 구현된다.

<br>

- 과정
    1. 비디오의 임의적인 위치 좌표 $x(x,y)$ 는 아래 함수(3D hash encoding function)를 거쳐 high-dimension features로 인코딩된다.
        - 3D hash encoding function: $γ_{3D}(x, t)$
            - 비디오를 다음과 같은 3차원 좌표 공간으로 표현할 수 있다. $x_{3D} : (x, y, t)$                  
                ![Untitled](https://i.ibb.co/N13MCNt/Untitled-6.png)
                
            - 3D hash encoding을 하여 비디오의  3차원 공간을 discrete한 $L$ 개의 multi-resolution feature grid로 표현한다.
                - 아래 그림에서는 multi-resolution feature grid가 8개의 resolution으로 구성되었다.                      
                    ![Untitled](https://i.ibb.co/zPPgxHT/Untitled-7.png)
                
            - 아래 수식은 $l$ 번째 layer의 resolution $N_l$ 을 나타내는 수식이다.                  
                ![Untitled](https://i.ibb.co/rGhc1JJ/Untitled-8.png)
                - $[N_{min}, N_{max}]$: coarsest and finest resolutions을 나타낸다.
                  
        
        <br>
        
    2. 그 다음 인코딩된 features는 아주 작은 MLP를 통해 canonical field에 해당하는 좌표 $x$’로 변환된다.
        - MLP $D : (γ_{3D}(x, t)) → x$′
          
            ![Untitled](https://i.ibb.co/9Tjbj1v/Untitled-9.png)
            

<br>

### 4.2.3. 최종 결과:

![Untitled](https://i.ibb.co/DpNcnYT/Untitled-10.png)

1. $D(γ_{3D}(x, t))$: 
    - **3차원 좌표 공간으로 표현된 비디오 $(x,t)$**를 **3D hash encoding function**에 넣어 discrete한 $L$ 개의 **multi-resolution feature grid**로 표현하고, MLP $D : (γ_{3D}(x, t)) → x$′를 거쳐 **canonical field의 2D 좌표**를 반환한다.  
    - [비디오 ⇒ multi-resolution feature grid ⇒ 2D 좌표 공간 (x’, y’)]  
        ![Untitled](https://i.ibb.co/1q31ZBS/Untitled-11.png)

    <br>
    
2. $C(D(γ_{3D}(x, t)))$: 
    - 2D 좌표(x’, y’)를 세밀한 정보도 캐치할 수 있도록 multi-resolution hash encoding한 feature 만들고, MLP를 거쳐 RGB 공간의 이미지를 반환한다.      
        ![Untitled](https://i.ibb.co/MnQGk46/Untitled-12.png)
    

<br>

## 4.3. Model Design

1. annelaed hash 적용
    - 문제:3D hash deformation의 능력이 강해질 수록 temporal deformation의 매끄러움이 약해진다는 trade-off가 존재한다.
    - 즉, canonical image의 semantic 정보가 약해지며, canonical image에 image algorithm을 입히기 어려워진다.
    - 따라서 canonical image의 semantic 정보를 유지하면서, 정확하게 비디오를 재구성(reconstruction)하기 위해서 **annealed multi-resolution hash encoding**을 적용하였다.
    - annealed hash 적용 전과 후의 모습          
        ![Untitled](https://i.ibb.co/rfYG0Lm/Untitled-13.png)
        
    
    <br>
    
2. Flow-guided Consistency Loss 도입
    - smoothness of deformation를 더욱 강화하기 위해서, **flow-based consistency**를 도입하였다.
    - 목적: 흐름(flow) 상에서 일치하는 좌표(point)들은 canonical field의 같은 좌표에 존재하도록 한다.
    
    <br>

3. Grouped Content Deformation Fields 도입
    - 비디오에 겹쳐져 가려진 부분이 많다거나, 복잡한 여러 물체들이 존재하는 경우에는, 추가적인 semantic information(semantic masks)을 사용하였다.
    - semantic masks는 grouped deformation field들을 결합한 mask이다.

<br>

## 4.4. Application to Consistent Video Processing

- canonical image $l_c$ 는 다양한 downstream algorithms에 사용된다.
- 아래 세 가지 SOTA 알고리즘을 사용하였다.
    1. ControlNet [69]: prompt-guided video-to-video translation.          
        ![Untitled](https://i.ibb.co/jR42B2h/Untitled-14.png)
        
    2. Segment-anything (SAM) [18]: video object tracking.          
        ![Untitled](https://i.ibb.co/4ZPy31N/Untitled-15.png)
        
    3. R-ESRGAN [61]: video super-resolution.          
        ![Untitled](https://i.ibb.co/B6sMrWS/Untitled-16.png)
        

# 5. Conclusion

지금까지 비디오 처리에서 temporal consistency를 달성하기 위한 방법으로 Content Deformation fields에 대해 알아보았으며, fidelity와 temporal consistency 측면에서 좋은 결과를 내었다.