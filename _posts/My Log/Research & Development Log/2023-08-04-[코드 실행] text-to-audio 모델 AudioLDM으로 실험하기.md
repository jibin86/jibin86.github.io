---
title:  "[코드 실행] text-to-audio 모델 AudioLDM으로 실험하기"
categories: [My Log, Research & Development Log]
tags: [diffusion, log]
typora-root-url: ../
---

- 내용: video dataset 조사한 것 중에 하나 선택해서 video caption을 input으로 했을 때 audio 결과가 어떤지 audioldm 모델로 결과 뽑고, video(caption에 해당하는)랑 audio(audioldm모델로 뽑은 결과) 를 합쳤을 때 두개가 sink가 얼마나 잘 안 맞는지 확인하기.
- 과정
    1. audioldm 환경 설정 & 실행
       
        ```python
        # Optional
        conda create -n audioldm python=3.8; conda activate audioldm
        # Install AudioLDM
        pip3 install audioldm
        
        ### Text-to-Audio Generation: generate an audio guided by a text
        # The default --mode is "generation"
        audioldm -t "A hammer is hitting a wooden surface" 
        # Result will be saved in "./output/generation"
        ```
        
        [GitHub - haoheliu/AudioLDM at dda0f54ab283ecdc1fe94ffc3182236cb8c343bf](https://github.com/haoheliu/AudioLDM/tree/dda0f54ab283ecdc1fe94ffc3182236cb8c343bf)
        
        - Input Text: `A hammer is hitting a wooden surface`
        - Output Audio (generated audio):
          
            <audio src="/../../images/2023-08-04-[논문 조사] Audio로 Image 편집하는 논문 있는지 조사하기 (copy)/미디어.wav" controls="true"></audio>
        
        <br>
       
    2. Video Dataset: Webvid에서 Video 4개 선정하고, Video의 caption을 넣고, Audio 생성
        - Input Text: `Travel blogger shoot a story on top of mountains. young man holds camera in forest.`
            - video
    
                <video height="180" width="288" controls>
                <source src="/../../images/2023-08-04-[논문 조사] Audio로 Image 편집하는 논문 있는지 조사하기 (copy)/0.mp4"/>
                </video>
                
                
                
            - generated audio
              
                <audio src="/../../images/2023-08-04-[논문 조사] Audio로 Image 편집하는 논문 있는지 조사하기 (copy)/미디어1.wav" controls="true"></audio>
            
        - Input Text: `Horse grazing - seperated on green screen`
            - video
              
                <video height="180" width="288" controls>
                <source <source src="/../../images/2023-08-04-[논문 조사] Audio로 Image 편집하는 논문 있는지 조사하기 (copy)/1.mp4"/>
                </video>
                
                
                
            - generated audio
              
                <audio src="/../../images/2023-08-04-[논문 조사] Audio로 Image 편집하는 논문 있는지 조사하기 (copy)/미디어2.wav" controls="true"></audio>
            
        - Input Text: `City traffic lights. blurred view`
            - video
              
              <video height="180" width="288" controls>
                <source <source src="/../../images/2023-08-04-[논문 조사] Audio로 Image 편집하는 논문 있는지 조사하기 (copy)/2.mp4"/>
                </video>
                
                
                
            - generated audio
              
                <audio src="/../../images/2023-08-04-[논문 조사] Audio로 Image 편집하는 논문 있는지 조사하기 (copy)/미디어3.wav" controls="true"></audio>
            
        - Input Text: `Young woman flexing muscles with barbell in gym.the coach helps her.`
            - video
              
              <video height="180" width="288" controls>
                <source <source src="/../../images/2023-08-04-[논문 조사] Audio로 Image 편집하는 논문 있는지 조사하기 (copy)/3.mp4"/>
                </video>
                
                
                
            - generated audio
        
                <audio src="/../../images/2023-08-04-[논문 조사] Audio로 Image 편집하는 논문 있는지 조사하기 (copy)/미디어4.wav" controls="true"></audio>

<br>