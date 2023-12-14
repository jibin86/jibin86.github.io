---
title:  "[논문 조사] Audio 2개를 mixing하거나, 이어붙이거나, 중첩시켜서 input으로 사용하는 생성모델 논문 조사"
categories: [My Log, Research & Development Log]
tags: [diffusion, log]
typora-root-url: ../
---

아래와 같이 세 가지 모델들을 찾아봤는데, 여러 오디오가 중첩된 비디오에서 오디오를 분리하는 과정(Audio separation)의 역과정에 대한 논문, 즉, Audio separation의 역과정인 분리된 오디오로 여러 오디오가 중첩된 비디오로 생성하는 모델은 현재까지 찾아본 바로는 아직 없는 것 같다.  

![Untitled 6](/../../images/2023-08-28-[논문 조사] audio 2개를 mixing하거나, 이어붙이거나, 중첩시켜서 input으로 사용하는 생성모델 논문 조사/Untitled 6.png)      
[VAST: Video-Audio Separation through Text](https://cs-people.bu.edu/rxtan/projects/VAST/)

<br>

### 1. Music Mixing Style Transfer: A Contrastive Learning Approach to Disentangle Audio Effects

[Papers with Code - Music Mixing Style Transfer: A Contrastive Learning Approach to Disentangle Audio Effects](https://paperswithcode.com/paper/music-mixing-style-transfer-a-contrastive)  
![Untitled](/../../images/2023-08-28-[논문 조사] audio 2개를 mixing하거나, 이어붙이거나, 중첩시켜서 input으로 사용하는 생성모델 논문 조사/Untitled.png)  

- 여러 오디오 트랙을 입력 받고, 스타일이 변형된 오디오를 생성해주는 구조이다.  
- 아래 링크의 AUDIO SAMPLES를 통해 합성 결과를 확인할 수 있다.  
    [MUSIC MIXING STYLE TRANSFER: https://jhtonykoo.github.io/MixingStyleTransfer/](https://jhtonykoo.github.io/MixingStyleTransfer/)      
    ![Untitled 1](/../../images/2023-08-28-[논문 조사] audio 2개를 mixing하거나, 이어붙이거나, 중첩시켜서 input으로 사용하는 생성모델 논문 조사/Untitled 1.png)

<br><br>

### 2. Sounding Video Generator: A Unified Framework for Text-guided Sounding Video Generation

[Papers with Code - Sounding Video Generator: A Unified Framework for Text-guided Sounding Video Generation](https://paperswithcode.com/paper/sounding-video-generator-a-unified-framework)  
- 텍스트를 입력 받았을 때, 사운드와 함께 비디오를 생성해주는 모델이다.      
    ![Untitled 2](/../../images/2023-08-28-[논문 조사] audio 2개를 mixing하거나, 이어붙이거나, 중첩시켜서 input으로 사용하는 생성모델 논문 조사/Untitled 2.png)  

- 찾고자 하는 두 가지의 오디오를 합치는 부분은 없지만, 오디오와 비디오를 함께 생성해주는 부분이 하고자하는 프로젝트와 유사하여 가지고 왔다.      
    ![Untitled 3](/../../images/2023-08-28-[논문 조사] audio 2개를 mixing하거나, 이어붙이거나, 중첩시켜서 input으로 사용하는 생성모델 논문 조사/Untitled 3-1702522783943-51.png)

- 아래 링크의 Sounding Video Samples 부분을 통해 Sample를 확인할 수 있다.      
    [https://github.com/jwliu-cc/svg](https://github.com/jwliu-cc/svg)      
    ![Untitled 4](/../../images/2023-08-28-[논문 조사] audio 2개를 mixing하거나, 이어붙이거나, 중첩시켜서 input으로 사용하는 생성모델 논문 조사/Untitled 4.png)

<br><br>

### 3. MovieFactory: Automatic Movie Creation from Text using Large Generative Models for Language and Images

[Papers with Code - MovieFactory: Automatic Movie Creation from Text using Large Generative Models for Language and Images](https://paperswithcode.com/paper/moviefactory-automatic-movie-creation-from)  

- 이 모델은 텍스트를 입력 받았을 때, cinematic-picture (3072×1280), film-style (multi-scene), and multi-modality (sounding) movies를 생성해주는 모델이다.
- 즉, 텍스트를 입력 받았을 때, 영화를 생성해주는 모델이다.
- 직접적으로 여러 오디오가 합쳐진 데이터를 입력 받지는 않지만, 아래 그림과 같이 텍스트 스크립트를 기반으로 비디오를 생성하고, 비디오 클립에 해당하는 오디오 부분을 검색(retrieve)를 통해 가져온다.      
    ![Untitled 5](/../../images/2023-08-28-[논문 조사] audio 2개를 mixing하거나, 이어붙이거나, 중첩시켜서 input으로 사용하는 생성모델 논문 조사/Untitled 5.png){: width="400"}      

- 샘플 영상은 아래 링크를 통해 확인할 수 있다.      
    [https://www.youtube.com/watch?v=tvDknhMFhzk](https://www.youtube.com/watch?v=tvDknhMFhzk){: width="500"}  

<br><br>

### 4. 기타: 오디오-비디오 관련 연구를 리뷰한 논문
**Learning in Audio-visual Context: A Review, Analysis, and New Perspective**  
[https://arxiv.org/pdf/2208.09579.pdf](https://arxiv.org/pdf/2208.09579.pdf)

- 오디오-비디오 관련 연구를 크게 3가지로 나누어 분석한 논문이다.
    1. **Audio-visual Boosting**
    2. **Cross-modal Perception**
    3. **Audio-visual Collaboration**      
    ![Untitled 7](/../../images/2023-08-28-[논문 조사] audio 2개를 mixing하거나, 이어붙이거나, 중첩시켜서 input으로 사용하는 생성모델 논문 조사/Untitled 7.png)

<br>

1. **Audio-visual Boosting  (Semantic Consistency)**
    1. Audio-Visual Recognition  
        ![Untitled 8](/../../images/2023-08-28-[논문 조사] audio 2개를 mixing하거나, 이어붙이거나, 중첩시켜서 input으로 사용하는 생성모델 논문 조사/Untitled 8.png)  
        
        - Speech Recognition
        - Speaker Recognition
        - Action Recognition
        - Emotion Recognition  
        
        <br>
        
    2. Uni-modal Enhancement          
        ![Untitled 9](/../../images/2023-08-28-[논문 조사] audio 2개를 mixing하거나, 이어붙이거나, 중첩시켜서 input으로 사용하는 생성모델 논문 조사/Untitled 9.png)          
        
        - Speech Enhancement
        - Speech Separation
        - Sound Separation
        - Face Super-resolution and Reconstruction  

    <br>
    
2. **Cross-modal Perception (Spatial Consistency)**  
    1. Cross-modal Generation  
        ![Untitled 10](/../../images/2023-08-28-[논문 조사] audio 2개를 mixing하거나, 이어붙이거나, 중첩시켜서 input으로 사용하는 생성모델 논문 조사/Untitled 10.png)  
        
        - Mono Sound Generation
        - Spatial Sound Generation
        - Video Generation
        - Depth Estimation  
        
        <br>
        
    2. Audio-visual Transfer Learning          
        ![Untitled 11](/../../images/2023-08-28-[논문 조사] audio 2개를 mixing하거나, 이어붙이거나, 중첩시켜서 input으로 사용하는 생성모델 논문 조사/Untitled 11.png) 
    
        <br>
        
    3. Cross-modal Retrieval          
        ![Untitled 12](/../../images/2023-08-28-[논문 조사] audio 2개를 mixing하거나, 이어붙이거나, 중첩시켜서 input으로 사용하는 생성모델 논문 조사/Untitled 12.png)
    
    <br>
    
3. **Audio-visual Collaboration (Temporal Consistency)**
    1. Audio-visual Representation Learning

        <br>

    2. Audio-visual Localization          
        ![Untitled 13](/../../images/2023-08-28-[논문 조사] audio 2개를 mixing하거나, 이어붙이거나, 중첩시켜서 input으로 사용하는 생성모델 논문 조사/Untitled 13.png)          
        
        - Sound Localization in Videos
        - Saliency Detection
        - Audio-visual Navigation 
        
        <br>
        
    3. Audio-visual Event Localization and Parsing          
        ![Untitled 14](/../../images/2023-08-28-[논문 조사] audio 2개를 mixing하거나, 이어붙이거나, 중첩시켜서 input으로 사용하는 생성모델 논문 조사/Untitled 14.png)  
    
        <br>
        
    4. Audio-visual Question Answering and Dialog          
        ![Untitled 15](/../../images/2023-08-28-[논문 조사] audio 2개를 mixing하거나, 이어붙이거나, 중첩시켜서 input으로 사용하는 생성모델 논문 조사/Untitled 15.png)
