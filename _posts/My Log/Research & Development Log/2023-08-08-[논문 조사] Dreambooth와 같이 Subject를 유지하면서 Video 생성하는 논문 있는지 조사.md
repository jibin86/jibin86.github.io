---
title:  "[논문 조사] Dreambooth와 같이 Subject를 유지하면서 Video 생성하는 논문 있는지 조사"
categories: [My Log, Research & Development Log]
tags: [diffusion, log]
typora-root-url: ../
---

### 1. Dreambooth 구조 ⇒ for customizing

[[논문 리뷰] DreamBooth: Fine Tuning Text-to-Image Diffusion Models for Subject-Driven Generation](https://jibin86.github.io/ai%20tech/computer%20vision/paper%20review/%EB%85%BC%EB%AC%B8-%EB%A6%AC%EB%B7%B0-DreamBooth-Fine-Tuning-Text-to-Image-Diffusion-Models-for-Subject-Driven-Generation/) 

- **Fine-Tuning** Text-to-Image diffusion
  
    ![image](/../../images/2023-08-08-[논문 조사] Dreambooth와 같이 Subject를 유지하면서 Video 생성하는 논문 있는지 조사/Untitled (12).png)
    
    1. low-resolution **text-to-image model**을 **fine-tuning**한다.
        1. **input image**와 **text prompt("A photo of a [T] dog”)** 쌍으로 diffusion model을 fine-tuning한다.
            - **text prompt**는 **unique identifier([T])**와 **class name(dog)**으로 구성된다.
        2. **class-specific prior preservation loss 적용**
           
            ![image](/../../images/2023-08-08-[논문 조사] Dreambooth와 같이 Subject를 유지하면서 Video 생성하는 논문 있는지 조사/Untitled (13).png)
            
            - 클래스 이름을 text prompt에 넣어도 (e.g., "A photo of a dog”) dog라는 클래스 prior가 유지될 수 있도록 위의 fine-tuning과 함께 학습한다.

        <br>

    2. super resolution components를 fine-tuning하여 높은 해상도 이미지를 얻는다.
        1. input images set의 **low-resolution과 high-resolution image쌍**으로 fine-tuning 
           
            ⇒ 작은 디테일에 대한 높은 정확도 유지 가능

    <br>
    
- Inference
  
    ![image](/../../images/2023-08-08-[논문 조사] Dreambooth와 같이 Subject를 유지하면서 Video 생성하는 논문 있는지 조사/Untitled (14).png)
    
    - **unique identifier**를 다른 문장들에 넣고, **personalized text-to-image** 모델을 통해 이미지를 생성한다.

<br>

### 2. 논문 조사:

- Dreamix: Video Diffusion Models are General Video Editors
  
    [https://arxiv.org/pdf/2302.01329.pdf](https://arxiv.org/pdf/2302.01329.pdf)
    
- Animate-A-Story: Storytelling with Retrieval-Augmented Video Generationd
  
    [https://arxiv.org/pdf/2307.06940.pdf](https://arxiv.org/pdf/2307.06940.pdf)
    
- DreamBooth-V
- Make-A-Protagonist: Generic Video Editing with An Ensemble of Experts
  
    [https://arxiv.org/pdf/2305.08850.pdf](https://arxiv.org/pdf/2305.08850.pdf)
    
- Align your Latents: High-Resolution Video Synthesis with Latent Diffusion Models
  
    [https://arxiv.org/pdf/2307.06940.pdf](https://arxiv.org/pdf/2307.06940.pdf)
    
- Text2Video-Zero: Text-to-Image Diffusion Models are Zero-Shot Video Generators
  
    [https://arxiv.org/pdf/2303.13439.pdf](https://arxiv.org/pdf/2303.13439.pdf)
    
- Rerender A Video: Zero-Shot Text-Guided Video-to-Video Translation

    [https://arxiv.org/pdf/2306.07954.pdf](https://arxiv.org/pdf/2306.07954.pdf)