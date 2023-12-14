---
title:  "[코드 실행] Multi-Concept Customization of Text-to-Image Diffusion 코드 실행 (메모리 부족 => 보류)"
categories: [My Log, Research & Development Log]
tags: [diffusion, log]
typora-root-url: ../
---

[https://github.com/adobe-research/custom-diffusion](https://github.com/adobe-research/custom-diffusion)

### 1. 코드 실행에 필요한 파일 다운로드 & 가상환경 생성 & 실행

```bash
### custom-diffusion 깃허브 클론
git clone https://github.com/adobe-research/custom-diffusion.git
cd custom-diffusion

### stable-diffusion 깃허브 클론
git clone https://github.com/CompVis/stable-diffusion.git
cd stable-diffusion

### stable-diffusion의 사전에 정의한 environment.yaml 파일에 따라 가상환경 생성
conda env create -f environment.yaml
conda activate ldm
pip install clip-retrieval tqdm
```

- `conda env create -f`: 기존의 환경 설정 파일을 사용하여 새로운 가상 환경을 생성한다.
    - `-f` 옵션은 환경 설정 파일을 나타내며, 일반적으로 YAML 형식으로 작성된 파일이다.
- `conda create -n`: 새로운 빈 가상 환경을 생성한다.
    - `-n` 옵션 다음에 새로운 가상 환경의 이름을 지정한다. 이 방법은 환경 설정 파일을 사용하지 않고 직접 가상 환경을 구성하는 경우에 사용된다.

```bash
### 모델을 저장한 디렉토리 생성
mkdir -p models/ldm/stable-diffusion-v1/
cd models/ldm/stable-diffusion-v1/

### Download the stable-diffusion model checkpoint 
wget https://huggingface.co/CompVis/stable-diffusion-v-1-4-original/resolve/main/sd-v1-4.ckpt
```

<br>

### 2. **Single-Concept Fine-tuning**

1. **Real images as regularization: regularization을 위한 이미지 생성**
   
    ```bash
    ## download dataset
    wget https://www.cs.cmu.edu/~custom-diffusion/assets/data.zip
    unzip data.zip
    ```
    
    ```bash
    ## run training (30 GB on 2 GPUs)
    bash scripts/finetune_real.sh "cat" data/cat real_reg/samples_cat  cat finetune_addtoken.yaml <pretrained-model-path>
    ```
    
    - fine-tuning **실행 시에 발생한 오류 사항들**
        1. `MisconfigurationException: You requested GPUs: [0, 1]
           But your machine only has: [0]`
            - `finetune_real.sh` 파일의 gpu 개수 수정
              
                ![image](/../../images/2023-08-08-[코드 실행] Multi-Concept Customization of Text-to-Image Diffusion 코드 실행 및 결과 확인/Untitled (15).png)
                
                ![image](/../../images/2023-08-08-[코드 실행] Multi-Concept Customization of Text-to-Image Diffusion 코드 실행 및 결과 확인/Untitled (16).png)
                
                - ⇒ `-t --gpus 0, \`
                    - 이때, 쉼표를 지우면, 아래 코드에서 int를 split하는 오류가 발생한다. 
                      ⇒ 쉼표를 유지한다.
                      
                        ![image](/../../images/2023-08-08-[코드 실행] Multi-Concept Customization of Text-to-Image Diffusion 코드 실행 및 결과 확인/Untitled (17).png)
                      
                        ![image](/../../images/2023-08-08-[코드 실행] Multi-Concept Customization of Text-to-Image Diffusion 코드 실행 및 결과 확인/Untitled (18).png)
           
        2. GPU 메모리를 모두 다 써 오류가 발생했다. => (보류)
           
            ```bash
            RuntimeError: CUDA out of memory. Tried to allocate 2.00 GiB (GPU 0; 11.74 GiB total capacity; 7.01 GiB already allocated; 213.69 
            MiB free; 8.71 GiB reserved in total by PyTorch) If reserved memory is >> allocated memory try setting max_split_size_mb to avoid 
            fragmentation.  See documentation for Memory Management and PYTORCH_CUDA_ALLOC_CONF
            ```
            
            ![image](/../../images/2023-08-08-[코드 실행] Multi-Concept Customization of Text-to-Image Diffusion 코드 실행 및 결과 확인/Untitled (19).png)
            

