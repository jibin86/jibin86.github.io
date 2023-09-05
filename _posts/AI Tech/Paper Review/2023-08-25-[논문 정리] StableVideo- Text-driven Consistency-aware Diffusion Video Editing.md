---
title:  "[논문 정리] StableVideo: Text-driven Consistency-aware Diffusion Video Editing"
categories: [AI Tech, Computer Vision, Paper Review]
tags: [diffusion]
use_math: true
---

![1](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/1f5cbdd3-f425-43d3-955e-4390b779dcfc)

![2](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/aff386d9-0719-490a-862a-133ea6fb19d8)

![3](https://github.com/jibin86/RealTimeFaceRecognition/assets/89712324/99a8d700-beb7-44b0-bf87-9b17706e3f6c)


오늘은 StableVideo 논문을 읽어보고자 한다.  
이 논문은 텍스트 기반으로 비디오를 수정하는 분야를 다룬 논문이다.  
논문의 목적과, 모델 구조를 중심으로 정리하였다.


[https://arxiv.org/pdf/2308.09592.pdf](https://arxiv.org/pdf/2308.09592.pdf)

- 저자가 무엇을 해내고 싶어했는가?
    - 비디오 에디팅 분야에서, **어떻게 하면 높은 시간적(temporal) 일관성 뿐만 아니라 target object의 기하학적(geometric) 일관성을 달성할 수 있을까?**
- 이 연구의 접근에서 중요한 요소는 무엇인가?
    1. **inter-frame propagation mechanism**
    2. **atlas aggregation network**

---

- (Warming up)      
    ![Untitled](https://i.ibb.co/8YTBYDL/Untitled.png)      
    - 위의 그림에서, “Orange SUV in sunny snow winter”라는 텍스트를 입력하면, 자동차(전경)와 배경이 위치적으로, 시간적으로 일관성을 유지한다는 것을 알 수 있다.

---

# 1. Abstract & Introduction

## 1.1. 기존 Diffusion 방식의 문제

- 기존 Diffusion 기반의 방식들은 비디오를 사실적으로 잘 생성하지만, 비디오 수정(editing) 분야에서, 수정 object에 대해 시간적(temporal), 기하학적(geometric) 일관성을 유지하지 못하는 단점이 있다.

## 1.2. 논문의 목표

- 어떻게 하면 높은 **시간적(temporal) 일관성** 뿐만 아니라 target object의 **기하학적(geometric) 일관성**을 달성할 수 있을까?

## 1.3. 논문에서 제안하고자 하는 부분 (Method)

1. 새로운 **inter-frame propagation mechanism**을 제안한다.
    - 이 매커니즘은 layered된 representations을 사용하여 수정된 프레임의 concept을 다음 프레임으로 전달(propagation)하는 역할을 한다.
    - 이 매커니즘을 통해 training, fine-tuning 없이, 좋은 propagation 결과를 얻을 수 있다.
    - 기존의 stable diffusion model 위에 이 매커니즘을 적용하였다.
2. **atlas aggregation network** 제안
    - 기존 NLA(Neural layered atlas) 방식인 직접 atlas를 수정하는 대신에, **key video frames**들을 수정하고 얻은 atlases를 **aggregation network**를 거쳐 **통합된(aggregated) atlas**를 얻는 방식을 사용한다.

- 위의 두 가지 방식을 적용한 모델을 **Stable Video**라고 정의하였다.

<br>

# 2. Related Work

## 2.2. Diffusion for Video Editing

- Tune-a-Video
    - 장점: video editing 분야에서 text-to-image model 사용하도록 하는 시발점?
    - 단점: temporal correlation이 제대로 고려되지 않아, 기하학적 일관성과 움직임(motion)의 일관성이 유지되지 못했다.
- Dreamix
    - 장점: 시간적 일관성을 유지하면서 움직임을 수정할 수 있는 text-to-video backbone 개발하였다.
    - 단점: 하지만 여전히 기하학적 일관성을 갖지 못한다.
    - 유사한 연구들
        - [8] Structure and content-guided video synthesis with diffusion models.
        - [40] Make-a-video: Text-to-video generation without text-video data.
        - [13] Imagen video: High definition video generation with diffusion models.
        - [54] Video probabilistic diffusion models in projected latent space.
        - [56] Efficient video generation with latent diffusion models.
        - [17] Video diffusion models.
- 논문이 제시한 StableVideo
    - 장점: 비디오를 수정할 때, 시간적, 기하학적 일관성 모두 유지할 수 있다.

<br>

## 2.3. Temporal Propagation in Video Editing

- Neural layered atlas (NLA)
    - 시간적 흐름에 따라 concept을 일관적으로 전달하기 위해, 비디오를 각 target에 대하여 통합된 2D atlas layers로 분해(decompose)하였다.
    - 장점: 시간적(termporal) 일관성을 달성하였다.
    - 작동 방식: 
    풀어쓰자면, Atlas는 비디오 안의 target object의 움직임을 2D layered atlases으로 표현한다.
    즉, 전체 비디오의 의미적으로 일치하는 픽셀들은 같은 atlas의 위치로 표현한다.
    그리고 이 2D layered atlases를 수정하고 이를 video로 변환하여, 수정된 비디오를 얻을 수 있다.
- Stable Video에서는 사전 학습된 NLA를 사용하여, temporal consistency를 달성하고자 한다.

<br>

# 3. Method

## Stable Video 파이프라인 요소들

![Untitled](https://i.ibb.co/6mX8Wm3/Untitled-1.png)

### 1. $g_b$: 배경 atlas를 수정하는 diffusion 모델

![Untitled](https://i.ibb.co/80XJC1m/Untitled-2.png)

**1.1. Condition ⇒ Depth map**

- 배경 atlas에서 MiDaS라는 depth를 예측하는 모델을 사용하여 depth map을 추출한다.
- depth 정보는 $g_b$ 가 움직임과 배경 간의 일관성을 유지할 수 있도록 하는 데에 사용된다.

**1.2. Condition ⇒ Text Prompt**

- cross-attention을 통해 디퓨전 모델의 조건으로 들어간다.
    
    ![Untitled](https://i.ibb.co/zfkmWFb/Untitled-3.png)
    
<br>

### 2. $g_f$: 전경 key frames를 수정하는 diffusion 모델

![Untitled](https://i.ibb.co/8cwQBQ0/Untitled-4.png)

**2.1. Condition ⇒ structure guidance (보라색 화살표)**

- structure guidance로 canny edge를 사용하며, concatenation하여 디퓨전 모델의 조건으로 들어간다.
    
    ![Untitled](https://i.ibb.co/V3Vdkqt/Untitled-5.png)
    
- $g_f$ 가 수정된 전경과 기존의 전경 간의 기하학적 일관성을 유지할 수 있도록 하는 데에 사용된다.
- $g_b$ 와 $g_f$ 는 같은 가중치를 공유하지만, 각각 다른 조건이 들어간다.

<br>

**2.2. Condition ⇒ Text Prompt**

- cross-attention을 통해 디퓨전 모델의 조건으로 들어간다.
    
    ![Untitled](https://i.ibb.co/zfkmWFb/Untitled-3.png)
    
<br>

## 전경(foreground)과 배경(background) 편집(editing)하는 방식

- 전경(foreground)과 배경(background)은 따로 편집된다.

### 전경(foreground)

1. **inter-frame propagation module을 사용하여 프레임의 concept을 전파한다. ⇒ 시간적, 기하학적 일관성 유지**
    
    ⇒ 3.2. Inter-frame Propagation (foreground editing)
    
    ![Untitled](https://i.ibb.co/xCDZTYr/Untitled-6.png)
    
2. **key frame editing 방식으로 Aggregation Network를 통해 atlas를 생성한다.**
    
    ⇒ 3.3. Aggregation Network
    
    ![Untitled](https://i.ibb.co/QdFZD7B/Untitled-7.png)

<br>

## 3.1. Problem Formulation

### NLA(Neural layered atlas)의 역할

- 프레임에서 다음 프레임으로 수정된 concept을 전파하는데, NLA가 사용된다.

### 논문을 이해하는데 필요한 NLA의 세부적인 수식과 과정들

1. 입력된 비디오를 foreground atlas와 background atlas로 분해한다.
    - **atlas**는 관련된 픽셀들을 묶어 요약한 representation이다.
    - $M^b(I_i), \space M^f(I_i),\space M^α(I_i)$: 픽셀 좌표 시스템(Pixel coordinate system)
        - 각각 **background, foreground, foreground opacity**에 해당하는 atlas들을 **픽셀(프레임)으로 매핑**해주는 네트워크이다.
        - 픽셀 좌표 시스템(Pixel coordinate system)이라고 불리며 다음과 같은 수식으로 표현할 수 있다.
            
            ![Untitled](https://i.ibb.co/R7CkmWv/Untitled-8.png)
            
            - $I$: 입력 비디오
            - $I_i$: 비디오의 각 프레임  

            <br>

- 아래 수식은 **Atlas representation**에서 **Pixel(프레임)**으로 매핑하는 수식이다.
(Atlas ⇒ Pixel Frame)
    
    ![Untitled](https://i.ibb.co/vHV2T5G/Untitled-9.png){: width="400"}
    
    - $A^b, A^f$: 각각 background와 foreground의 **atlas representation**
    - $B_i, F_i$: 각각 background, foreground atlas representation 부분에 해당하는 **픽셀**

    <br>

- 전체 비디오가 재구성되는 수식
    
    ![Untitled](https://i.ibb.co/zP7rJyM/Untitled-10.png)
    
    - 요약하자면, 생성된 atlas representation $A$ 을 해당 픽셀 좌표(프레임)로 변환하고. $\alpha_i$를 이용해서 배경(background)과 전경(foreground)을 합쳐 편집된 비디오를 완성한다.

<br>

## 3.2. Inter-frame Propagation (foreground editing)

![Untitled](https://i.ibb.co/4TTnQrk/Untitled-11.png)

- **Inter-frame Propagation**은 한 프레임에서 다음 프레임으로 concept을 전달(propagation)하는 역할을 한다.
- structure conditions $C_{i}$만 추가하는 것은 temporally consistent geometry를 보장할 수 없다.
    
    ⇒ 따라서 이 논문에서는 **conditional denoising process**를 통해 diffusion model이 **현재 프레임에 대한 구조(structure)** $C_{i}$와 **이전 프레임의 appearance 정보 $\hat{E}_i$** 를 고려할 수 있도록 한다.
    
- inter-frame propagation은 foreground object에만 적용한다.

<br>

### Inter-frame propagation의 세부 과정 (수식을 기반으로 정말 세세하게 작성하였다)

1. 비디오 $I$ 에서 key frame을 N개 뽑는다.     
    ![Untitled](https://i.ibb.co/QYX80h7/Untitled-12.png)      
    - key frame을 뽑는 조건 2가지
        1. 인접한 프레임들 간의 겹침이 상당해야한다.
        2. key 프레임들로 foreground object의 모든 모습들을 모두 표현할 수 있어야한다.

    <br>
    
2. 첫 (key) 프레임의 **structure $C_0$** (보라색 점선)와 **Text Prompt**를 diffusion 모델에 넣어 수정된 첫 프레임 $E_0$을 얻는다. 
    - $E_0=g^f(T,C_0)$
        
        ![Untitled](https://i.ibb.co/VL8BYZF/Untitled-13.png)

    <br>
        
3. 그 다음  $(UV_{0}^f)^{-1}$ 를 거쳐 프레임(픽셀)을 첫 프레임의 atlas $A^f_{0}$로 변환한다.
    
    $A^f_{0}=(UV^f_{0})^{-1}(E_0)$
    
    ![Untitled](https://i.ibb.co/pZf2DWy/Untitled-14.png)
    
    <br>
    
4. 그 다음, $\alpha_1 \space ◦ \space UV_{1}^f$ 를 거쳐 atlas $A^f_{0}$ 을 불완전한(incomplete) 다음 프레임 $\hat{E}_1$ 을 얻는다.
    
    $\hat{E}\_1=\alpha_1 \space ◦ \space UV_{1}^f(A^f_{0})$ 
    
    ![Untitled](https://i.ibb.co/gw38k2C/Untitled-15.png)

    <br>

5. 그 다음 key 프레임부터는 아래의 순서가 반복된다. 현재 (key) 프레임 인덱스가 $i-1$ 이라고 하며, 불완전한(incomplete) 현재 프레임 $\hat{E}_{i-1}$ 을 얻었다고 하자.

    <br>

6. 불완전한(incomplete) 현재 프레임 $\hat{E}\_{i-1}$ 을 현재 프레임의 structure $C_{i-1}$와 함께 diffusion model을 통과시켜 사실적인 현재 프레임 $E_{i-1}$ 을 얻는다.
    - 이때 SDEdit와 ILVR처럼 추정된 현재 프레임 $\hat{E}_{i-1}$ 에 noise를 일부 추가하고, 이를 denoising하여 더욱 정확한 output을 얻는다.
        
        ![Untitled](https://i.ibb.co/9q55NCG/Untitled-16.png)
        
        ![Untitled](https://i.ibb.co/V3Vdkqt/Untitled-5.png)

    <br>
        
7. 그 다음  $(UV_{i-1}^f)^{-1}$ 를 거쳐 프레임(픽셀) $E_{i-1}$을 현재 프레임의 atlas $A^f_{t-1}$로 변환한다.
    
    ![Untitled](https://i.ibb.co/wppKShf/Untitled-17.png)
    
    ![Untitled](https://i.ibb.co/q55GJTP/Untitled-18.png)

    <br>
    
8. 그 다음, $\alpha_i \space ◦ \space UV_{i}^f$ 를 거쳐 atlas $A^f_{i-1}$ 을 불완전한(incomplete) 다음 프레임 $\hat{E}_i$으로 변환한다.
    
    ![Untitled](https://i.ibb.co/TLVZBdG/Untitled-19.png)

    <br>
    
9. 6번~8번 과정을 반복하여 N개의 key frame에 대한 수정된 N개의 key frame을 얻는다.

<br>

### Inter-frame propagation 과정에서 등장하는 Diffusion model에 대한 세부 사항

- 불완전한 프레임 $\hat{E}_i$ 를 diffusion model에 넣고 완전한 프레임 $E_i$을 얻는 과정
    1. VQ-VAE으로 불완전한 프레임 $\hat{E}_i$ 을 인코딩하여 latent representation $\hat{Z}_i$ 를 얻는다.
    2. 얻은 latent representation $\hat{Z}_i$ 에 노이즈를 추가한다. (Variance Preserving Stochastic Differential Equation (VP-SDE))          
        ![Untitled](https://i.ibb.co/t3CYTSC/Untitled-20.png)          
        - $σ(t_0), \space α(t_0)$  는 평균과 분산을 조절해주는 scalar function이며, 노이즈의 강도를 조절하는 하이퍼파라미터이다. (ddpm에서는 베타 스케줄링이라고 불렀던 것 같다.)
    3. Text prompt $T$와 structure guidance $C_i$를 기반으로 $\hat{Z}_i(t_0)$ 을 denoising한다. 
    4. 마지막으로 latent representation $Z_i$ 을 디코딩하여 $E_i$ 을 얻는다.

<br>

## 3.3. Aggregation Network

![Untitled](https://i.ibb.co/ckM8L6t/Untitled-21.png)  
- Inter-frame propagation에서 수정된 N개의 key frame들을 Aggregation Network으로 통합한다.
- aggregation network는 단순하지만 효과적인 **two-layer 2D convolution network with skip connection**으로 구현된다.
- Aggregation Network는 통합되기 전 atlas와 통합된 atlas가 거의 일치되도록 학습된다.
    - 즉, 통합되기 전 key frame들(**Edited Frames**)과 통합된 atlas에서 key frame과 같은 시점의 frames들(**Reconstructed Frames**)이 같아지는 방향으로 학습된다.  
        ![Untitled](https://i.ibb.co/s9KKQ5C/Untitled-22.png)
        
<br>

## 3.4. Background Editing Process & Edited Foreground object와 합치는 Process

![Untitled](https://i.ibb.co/MRKDG8r/Untitled-23.png)

### (Edited Background Atlas 얻는) Background Editing 세부 과정

1. $(UV^b)^{-1}$ 을 통해, 비디오 프레임들(픽셀)에서 Background Atlas를 얻는다.
2. 얻은 Atlas에서 depth estimate 모델인 MiDaS를 통해 Depth Map을 얻는다.
3. Depth Map은 Background Atlas와 concat 되어 diffusion model의 조건으로 들어가고,
Text Prompt는 cross-attention 기법으로 diffusion model의 조건으로 들어간다.
4. diffusion model의 Output으로 Edited Background Atlas를 얻는다.

<br>

### Edited Background Atlas와 Edited Foreground Atlas를 합쳐 Edited Video를 생성하는 과정

![Untitled](https://i.ibb.co/KswZnBj/Untitled-24.png)

- Aggregation Network를 통해 Edited Foreground Atlas를 가져왔고, 
Background Editing에서 Edited Background Atlas를 얻었다고 가정해보자.
1. Edited Foreground Atlas에서 각 프레임을 $\alpha \space ◦ \space UV^f$ 을 통해 atlas에서 픽셀 프레임으로 각각 변환한다.
    - Edited Foreground Atlas에는 모든 프레임에 대한 정보를 담고 있다.
2. Edited Background Atlas도 마찬가지로 각 프레임을 $UV^b$ 을 통해 atlas에서 픽셀 프레임으로 각각 변환한다.
3. 1번과 2번에서 변환된 프레임들을 각각 합쳐 Edited Video를 완성한다.

<br>

# 5. Limitations and Future Works

- Limitations
    - 논문에서 제시하는 방식은 NLA를 사용하는 것에 한정되었다.
        - Atlas layer를 학습하는 것은 아래와 같이 상당한 구조적 변형이 일어났을 때, 좋은 성능을 내지 못 할 수도 있다.
            - ⇒ 자동차가 이동하는 영상을 확인해보니, 타이어가 회전하지 않고 그대로 앞으로 이동하는 모습을 볼 수 있었다.
            
            ![Untitled](https://i.ibb.co/kmgdq08/Untitled-25.png)
            
            - 이러한 문제는 비디오를 작은 클립으로 나누는 방법도 고려해봤지만, 매번 경우를 나누는 것은 현실적으로 불가능하다고 한다.
- Future Works
    - 논문에서 진행한 방식은 diffusion model을 fine-tuning 하지 않고, 비디오를 편집하는 방법을 다뤘다.
    - 수정된 비디오에 대해서 diffusion model를 fine-tuning을 했다면 더 좋은 결과를 낼 수 있었을 것이고, 이 부분을 Future works으로 남겼다.

<br>

# 6. Conclusion

- 지금까지 text 기반의 diffusion model을 활용하여 비디오를 수정하는 방식을 다뤄봤다.
- 논문에서 해결하고자하는 문제와 목표는 **foreground object editing에서 기하학적, 외적 일관성 문제**를 다루고 이를 해결하는 것이었다.
- 논문에서 제시한 방법은 **inter-frame propagation mechanism**과 **atlas aggregation network**을 비디오 수정 Task에 적용하는 것이다.
- 결과적으로 SOTA를 달성하였다.