---
title:  "[논문 정리] Custom Diffusion: Multi-Concept Customization of Text-to-Image Diffusion"
categories: [AI Tech, Computer Vision, Paper Review]
tags: [diffusion]
typora-root-url: ../
---

[https://arxiv.org/pdf/2212.04488.pdf](https://arxiv.org/pdf/2212.04488.pdf)

**Custom Diffusion의 구조를 빠르게 파악하기 위해** Method 중심으로 **논문의 핵심 문장을 인용**하며 정리하였다.

# 1. **Custom Diffusion**

- We propose Custom Diffusion, an efficient method for augmenting existing text-to-image models.
- We find that only optimizing a few parameters in the text-to-image conditioning mechanism is sufficiently powerful to represent new concepts while enabling fast tuning.
- Additionally, we can jointly train for multiple concepts or combine multiple fine-tuned models into one via closed-form constrained optimization.
- Our fine-tuned model generates variations of multiple new concepts in novel unseen settings.

<br>

## 1.1. **Pipeline**

![Untitled](https://i.ibb.co/tx1c5D9/Untitled.png)

1. target 이미지의 캡션과 유사한 캡션으로 이미지를 얻는다. 이 이미지는 fine-tuning시에 dog라는 class 특성을 잃지 않도록 해주는 역할을 한다.
2. fine-tuning: stable diffusion에서 학습했던 과정, loss와 마찬가지로 cross-attention block의 key와 value를 업데이트한다.

<br>

## 1.2. **CustomConcept101 dataset**

- We also introduced a new dataset of 101 concepts for evaluating model customization methods along with text prompts for single-concept and multi-concept compositions. For more details and results please refer to  the [dataset webpage](https://www.cs.cmu.edu/~custom-diffusion/dataset.html) and [code](https://github.com/adobe-research/custom-diffusion/tree/main/customconcept101).

<br>

# 2. Method

![Untitled](https://i.ibb.co/tx1c5D9/Untitled.png)

- only updates a small subset of weights in the cross-attention layers of the model.
- we use a regularization set of real images to prevent overfitting on the few training samples of the target concepts.

<br>

## 2.1. Single-Concept Fine-tuning

- we use Stable Diffusion as our backbone model, which is built on the Latent Diffusion Model (LDM).
  1. 이미지를 잠재공간으로 인코딩하기
      - LDM first encodes images into a latent representation, **using hybrid objectives** of VAE, Patch-GAN and LPIPS
  2. 잠재공간에서 Diffusion model 학습하기
      - They then train a diffusion model on the latent representation with text condition injected in the model using cross-attention.

<br>

### 2.1.1. Learning objective of diffusion models.

- Diffusion models aim to approximate the original data distribution $q(x_0)$ with $p_θ (x_0)$      
    ![Untitled](https://i.ibb.co/rvYQ876/Untitled-1.png)
    
    - $x_1$ to $x_T$ are latent variables of a forward Markov chain s.t. $x_t = \sqrt{α_t}x_0 + \sqrt{1 − α_t}ϵ.$  
      
      <br>

    - Train
        - The model learns the reverse process of a fixed-length (usually 1000) Markov chain.
          
            ![Untitled](https://i.ibb.co/hs1c02w/Untitled-2.png)
            
            - $ϵ_θ$ is the model prediction
            - $w_t$ is a time-dependent weight on the loss.
            - $c$: any other modality
        - Problem
            - This can be computationally inefficient for large-scale models and can easily lead to overfitting when training on a few images.
        - Solution
            - we aim to identify a minimal set of weights that is sufficient for the task of fine-tuning.

<br>

### 2.1.2. Rate of change of weights.

- fine-tuned model의 각 레이어에 대해서 파라미터 변화 살펴봄
    - we analyze the change in parameters for each layer in the fine-tuned model on the target dataset with the loss in Eqn. 2
      
        ![Untitled](https://i.ibb.co/hs1c02w/Untitled-2.png)
        
        ![Untitled](https://i.ibb.co/f2kmPYq/Untitled-3.png)
        
        - $θ^′_l$ : updated pretrained model parameters of layer $l$
        - $θ_l$ : pretrained model parameters of layer $l$  

        <br>

    - 레이어의 종류 세 가지:
        1. cross-attention (between the **text** and **image**)
        2. self-attention (within the **image** itself)
        3. the rest of the parameters, including convolutional blocks and normalization layers in the diffusion model U-Net.
        - 각 레이어에 대한 $∆l$ 의 평균 비교              
            ![Untitled](https://i.ibb.co/9vF1c7r/Untitled-4.png)              
            - the cross-attention layer parameters have relatively higher ∆ compared to the rest of the parameters.
            - 심지어, cross-attention layers are only 5% of the total parameter count in the model.
            - **결론: Cross-Attention layer가 fine-tuning할 때에 상대적으로 중요한 역할을 한다!!**

<br>

### 2.1.3. Model fine-tuning.

- cross-attention이 하는 일
    - Cross-attention block **modifies the latent features** of the network according to the condition features.
    - ⇒ updating the mapping from given text to image distribution.  

    <br>

- cross-attention 수식:
    - text features $c ∈ R^{s×d}$ and latent image features $f ∈ R^{(h×w)×l}$ ,
    - a single-head cross-attention operation consists of $Q = W^qf , K = W^kc, V = W^v c$ ,
        - text ⇒ $K = W^kc, V = W^v c$
        - image ⇒ $Q = W^qf$
    - a weighted sum over value features as:          
        ![Untitled](https://i.ibb.co/K9h8T2T/Untitled-5.png)
        
        - $d^′$ is the output dimension of key and query features.  

    <br>

- we propose to only update $W_k$ and $W_v$ parameters of the diffusion model during the fine-tuning process.
    - 이유: 텍스트가 들어갔을 때, 특정한 이미지가 나오도록 학습하는 것인데, 텍스트는 $W^k$ and $W^v$ 에만 관여하므로, $W^k$ and $W^v$ 만 학습해도 된다.
      
        (text features are only input to $W^k$ and $W^v$ projection matrix in the cross-attention block.)
        
        ![Untitled](https://i.ibb.co/3kkpFMR/Untitled-6.png)
        
        ![Untitled](https://i.ibb.co/r0v4jq2/Untitled-7.png)
        

<br>

### 2.1.4. Text encoding.

- we introduce a new modifier token embedding ⇒ $V^∗ dog.$
- $V^∗$ 를 학습하는 방법
    1. $V^∗$ is **initialized** with a rare occurring token embedding.
    2. $V^∗$ is **optimized** along with cross-attention parameters.

<br>

### 2.1.5. Regularization dataset.

- Fine-Tuning 문제
    - Fine-tuning on the target concept and text caption pair can lead to **the issue of language drift.**
    - ex 1)  **training on “moongate”** will lead to the model **forgetting the association of “moon” and “gate”** with their previously trained visual concepts
      
        ![Untitled](https://i.ibb.co/NW5VGqC/Untitled-8.png)
        
    - ex 2) training on a personalized concept of **V∗ tortoise plushy** can leak, **causing all examples with plushy to produce the specific target images**

<br>

- Fine-Tuning 문제 해결 방법
    - we select a set of **200 regularization images** from the LAION-400M [69] dataset **with corresponding captions that have a high similarity with the target text prompt.**
        - target text prompt와 데이터셋의 caption과의 유사도 threshold 0.85 넘는 것만 선정
        (above threshold 0.85 in CLIP text encoder feature space.)
    

<br>

## 2.2. Multiple-Concept Compositional Fine-tuning

### 2.2.1. Joint training on multiple concepts.

- 학습 방법
    - we combine the training datasets for each individual concept and **train them jointly** with our method.
      
        ![Untitled](https://i.ibb.co/fqqG75k/Untitled-9.png)
        
    - $V^∗_i$ ($V^∗_1, V^∗_2, V^∗_3, ... )$ 를 학습하는 방법
        1. $V^∗_i$ are **initialized** with different rarely-occurring tokens.
        2. $V^∗$ are **optimized** along with cross-attention key and value matrices for each layer.
        - **Q는 frozen**하고 **K와 V만 업데이트**한다.
            - restricting the weight update to cross-attention **key** and **value** parameters leads to significantly better results for composing two concepts compared to methods like DreamBooth, which fine-tune all the weights.

<br>

### 2.2.2. Constrained optimization to merge concepts.

- 이전까지 target 텍스트에 대한 K와 V를 업데이트 하는 법을 배웠다. 
  이 부분에서는 regulation 텍스트에 대한 K와 V를 일정하게 유지하도록 학습하는 법을 다룬다.      
    ![Untitled](https://i.ibb.co/PtTX8GV/Untitled-10.png)
  
- $\{W^k_{0,l}, W^v_{0,l}\}^L_{l=1}$ : the key and value matrices for all $L$ cross-attention layers in the **pretrained model**.
    - 간략화 ⇒ $W_{0}$  : key and value matrices of **pretrained model**  

    <br>

- $\{W^k_{n,l}, W^v_{n,l}\}^L_{l=1}$ : the corresponding **updated matrices** for added concept $n ∈ \{1 · · · N \}$.
    - 간략화 ⇒ $W$ :  **key and value matrices of updated model**

    <br>

- $C ∈ R^{s×d}$ is the **text features** of dimension $d$, and target words $s$
    - all captions for each concept flattened out and concatenated.

<br>

- $C_{reg} ∈ R^{s_{reg}×d}$ consists of **text features of** ∼ 1000 randomly sampled captions for **regularization**.

<br>

### 2.2.3. Training details.

- details
    - We train with our method for
        - **250 steps in single-concept**,
        - **500 steps in two-concept joint training**
    - batch size of 8
    - learning rate $8 × 10^{−5}$
    - randomly **resize** the target images from $0.4 − 1.4×$ and append the prompt **“very small”, “far away” or “zoomed in”, “close up”** accordingly to the text prompt based on resize ratio.
- 결과
    - This leads to faster convergence and improved results.
    

<br>

# 3. Results

## 3.1. Single-Concept Results

![Untitled](https://i.ibb.co/sgFnz34/Untitled-11.png)

## 3.2. **Multi-Concept Results**

![Untitled](https://i.ibb.co/PFrfwC3/Untitled-12.png)

## 3.3. **Sample Qualitative Comparison with Concurrent Works**

![Untitled](https://i.ibb.co/MPbBGfh/Untitled-13.png)

![Untitled](https://i.ibb.co/hX5VhGM/Untitled-14.png)

## 3.4. **Model Compression**

![Untitled](https://i.ibb.co/d4PbYzK/Untitled-15.png)

<br>

# 4. **Limitations**

- Difficult compositions, e.g., a pet dog and a pet cat, remain challenging.
- In many case, the pre-trained model also faces a similar difficulty, and we believe that our model inherits these limitations.
- Additionally, composing increasing three or more concepts together is also challenging.

![Untitled](https://i.ibb.co/K29MWGG/Untitled-16.png)

- First column shows the sample target images used for fine-tuning the model with our joint training method.
- Second column shows the failed compositional generation by our method.
- Third column shows generations from the pretrained model with similar text prompt as input.