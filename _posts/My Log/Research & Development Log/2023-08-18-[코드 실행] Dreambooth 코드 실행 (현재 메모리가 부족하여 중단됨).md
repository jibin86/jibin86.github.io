---
title:  "[코드 실행] Dreambooth 코드 실행 (현재 메모리가 부족하여 중단됨)"
categories: [My Log, Research & Development Log]
tags: [diffusion, log]
typora-root-url: ../
---

[DreamBooth: https://dreambooth.github.io/](https://dreambooth.github.io/)

### **Preparation**

1. 깃허브 클론 후, ldm 가상환경 생성한다.
   
    ```bash
    ### Dreambooth 깃허브 클론
    git clone https://github.com/XavierXiao/Dreambooth-Stable-Diffusion.git
    cd Dreambooth-Stable-Diffusion
    
    ### environment.yaml 파일에 따라 가상환경 생성
    conda env create -f environment.yaml
    conda activate ldm
    ```
    
2. Pre-trained Stable diffusion model의 weight을 다운받는다.
    1. To fine-tune a stable diffusion model, you need to obtain the pre-trained stable diffusion models following their [instructions](https://github.com/CompVis/stable-diffusion#stable-diffusion-v1).
    2. Hugging Face에서 `sd-v1-4-full-ema.ckpt` 다운로드하고 아래의 경로에 저장
       
        ![Untitled](/../../images/2023-08-18-[코드 실행] Dreambooth 코드 실행 (현재 메모리가 부족하여 중단됨)/Untitled.png)

        
        - `/home/baek/sjb/Dreambooth-Stable-Diffusion/weight/sd-v1-4-full-ema.ckpt`
3. Regularization을 위한 데이터셋 생성하기.
    1. We also need to create a set of images for regularization, as the fine-tuning algorithm of Dreambooth requires that.
    2. The text prompt for generating regularization images can be `photo of a <class>`, where `<class>` is a word that describes the class of your object, such as `dog`. The command is
       
        ```bash
        python scripts/stable_txt2img.py --ddim_eta 0.0 --n_samples 8 --n_iter 1 --scale 10.0 --ddim_steps 50 --ckpt /path/to/original/stable-diffusion/sd-v1-4-full-ema.ckpt --prompt "a photo of a <class>"
        ```
        
        - 위의 명령어를 아래와 같이 수정
            - ckpt 경로 변경 `weight/sd-v1-4-full-ema.ckpt`
            - config 경로 변경 `configs/stable-diffusion/v1-inference.yaml`
            
            ```bash
            python scripts/stable_txt2img.py --ddim_eta 0.0 --n_samples 8 --n_iter 1 --scale 10.0 --ddim_steps 50 --ckpt weight/sd-v1-4-full-ema.ckpt --prompt "a photo of a <class>" --config configs/stable-diffusion/v1-inference.yaml
            ```
            
    - **Error 1:** TypeError 발생 (해결O)
      
        ![Untitled 1](/../../images/2023-08-18-[코드 실행] Dreambooth 코드 실행 (현재 메모리가 부족하여 중단됨)/Untitled 1.png)

        
        ![Untitled 2](/../../images/2023-08-18-[코드 실행] Dreambooth 코드 실행 (현재 메모리가 부족하여 중단됨)/Untitled 2.png)

        
        ![Untitled 3](/../../images/2023-08-18-[코드 실행] Dreambooth 코드 실행 (현재 메모리가 부족하여 중단됨)/Untitled 3.png)

        
        - 추가적으로 클론한 원본 stable-diffusion의 ddpm 코드와 dreambooth의 ddpm 코드 간의 차이가 있어 발생한 오류인 것 같다.
            - 방법: 클론한 stable-diffusion를 제거하고, dreambooth 내부에 있는 ddpm을 사용하도록 한다
    - **Error 2:** 모델 실행 중에 중단되는(Killed) 오류 발생 (해결X)
      
        ![Untitled 4](/../../images/2023-08-18-[코드 실행] Dreambooth 코드 실행 (현재 메모리가 부족하여 중단됨)/Untitled 4.png)

        
        - 찾아보니, GPU의 메모리가 부족하여 중단되는 오류라고 한다.
          
            ![Untitled 5](/../../images/2023-08-18-[코드 실행] Dreambooth 코드 실행 (현재 메모리가 부족하여 중단됨)/Untitled 5.png)

            
            - 이전에 사용한 GPU 메모리는 12GB이지만, 현재 사용하고 있는 GPU 메모리는 4GB이다.
                - 이전
                  
                    ```bash
                    Thu Jul 27 18:56:48 2023       
                    +---------------------------------------------------------------------------------------+
                    | NVIDIA-SMI 535.54.03              Driver Version: 535.54.03    CUDA Version: 12.2     |
                    |-----------------------------------------+----------------------+----------------------+
                    | GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
                    | Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
                    |                                         |                      |               MIG M. |
                    |=========================================+======================+======================|
                    |   0  NVIDIA GeForce RTX 3060        Off | 00000000:2B:00.0  On |                  N/A |
                    |  0%   42C    P8              24W / 170W |   1621MiB / 12288MiB |      6%      Default |
                    |                                         |                      |                  N/A |
                    +-----------------------------------------+----------------------+----------------------+
                    ```
                    
                - 현재
                  
                    ```bash
                    Thu Aug 17 15:49:31 2023       
                    +-----------------------------------------------------------------------------+
                    | NVIDIA-SMI 525.125.06   Driver Version: 525.125.06   CUDA Version: 12.0     |
                    |-------------------------------+----------------------+----------------------+
                    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
                    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
                    |                               |                      |               MIG M. |
                    |===============================+======================+======================|
                    |   0  NVIDIA GeForce ...  Off  | 00000000:01:00.0  On |                  N/A |
                    | 35%   31C    P5    19W / 100W |    392MiB /  4096MiB |     23%      Default |
                    |                               |                      |                  N/A |
                    +-------------------------------+----------------------+----------------------+
                                                                                                   
                    +-----------------------------------------------------------------------------+
                    | Processes:                                                                  |
                    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
                    |        ID   ID                                                   Usage      |
                    |=============================================================================|
                    |    0   N/A  N/A      1841      G   /usr/lib/xorg/Xorg                213MiB |
                    |    0   N/A  N/A      2071      G   /usr/bin/gnome-shell               63MiB |
                    |    0   N/A  N/A      8270      G   ...283772729983153826,262144      113MiB |
                    +-----------------------------------------------------------------------------+
                    ```
            
        - 방법1: **작은 사이즈의 모델을 다운받아 실행하기**
            1. Hugging Face에서 `sd-v1-4.ckpt` 다운로드하고 아래의 경로에 저장하기                
                ![Untitled 6](/../../images/2023-08-18-[코드 실행] Dreambooth 코드 실행 (현재 메모리가 부족하여 중단됨)/Untitled 6.png)  
                
                - `/home/baek/sjb/Dreambooth-Stable-Diffusion/weight/sd-v1-4.ckpt`
                
                <br>
                
            2. Regularization을 위한 데이터셋 생성하기
               
                ```bash
                python scripts/stable_txt2img.py --ddim_eta 0.0 --n_samples 8 --n_iter 1 --scale 10.0 --ddim_steps 50 --ckpt weight/sd-v1-4.ckpt --prompt "a photo of a <class>" --config configs/stable-diffusion/v1-inference.yaml
                ```
                
            - 결과: Killed
                ![Untitled 7](/../../images/2023-08-18-[코드 실행] Dreambooth 코드 실행 (현재 메모리가 부족하여 중단됨)/Untitled 7.png)
            
            <br>
            
        - 방법2: **배치 사이즈를 줄여보자**              
            ![Untitled 8](/../../images/2023-08-18-[코드 실행] Dreambooth 코드 실행 (현재 메모리가 부족하여 중단됨)/Untitled 8.png)  
        
            ```bash
            python scripts/stable_txt2img.py --ddim_eta 0.0 --n_samples 8 --n_iter 1 --scale 10.0 --ddim_steps 50 --ckpt weight/sd-v1-4.ckpt --prompt "a photo of a <class>" --config configs/stable-diffusion/v1-inference.yaml
            ```
            
            - n_samples 8 ⇒ 3
              
                ```bash
                python scripts/stable_txt2img.py --ddim_eta 0.0 --n_samples 3 --n_iter 1 --scale 10.0 --ddim_steps 50 --ckpt weight/sd-v1-4.ckpt --prompt "a photo of a <class>" --config configs/stable-diffusion/v1-inference.yaml
                ```
                
                - 결과: Killed                
                    ![Untitled 9](/../../images/2023-08-18-[코드 실행] Dreambooth 코드 실행 (현재 메모리가 부족하여 중단됨)/Untitled 9.png)
        
            - n_samples 3 ⇒ 1
              
                ```bash
                python scripts/stable_txt2img.py --ddim_eta 0.0 --n_samples 1 --n_iter 1 --scale 10.0 --ddim_steps 50 --ckpt weight/sd-v1-4.ckpt --prompt "a photo of a <class>" --config configs/stable-diffusion/v1-inference.yaml
                ```
                
                - 결과: Killed                    
                    ![Untitled 10](/../../images/2023-08-18-[코드 실행] Dreambooth 코드 실행 (현재 메모리가 부족하여 중단됨)/Untitled 10.png)
            
        - 방법3: **구글 Colab Pro를 결제했기에, Colab에서 다시 실행해보기 (진행중)**