---
title:  "[코드 실행] Make A Stable Diffusion Video의 Inference 코드 실행 (보류)"
categories: [My Log, Research & Development Log]
tags: [diffusion, log]
typora-root-url: ../
---

https://github.com/lxj616/make-a-stable-diffusion-video

1. sjb 폴더 이동 후, git 클론

   - `git clone <https://github.com/lxj616/make-a-stable-diffusion-video.git`>

2. conda 가상환경 생성

   - `conda create -n make_a_stable_diffusion_video_sjb`
   - `conda activate make_a_stable_diffusion_video_sjb`

3. inference 코드 실행

   - `python3 examples/research_projects/make_a_stable_diffusion_video/run_inference_video.py`

   - 에러 발생 ⇒ `ImportError: cannot import name 'StableDiffusionVideoInpaintPipeline' from 'diffusers'`

     - `diffusers` 의 conda 패키지와 저자의 `diffusers` 패키지와 이름이 같고, 구성이 매우 유사하여, conda에 설치된 `diffusers` 패키지를 지우고, 저자의 diffusers를 sys.path에 추가하였다.

       ```jsx
       import sys
       sys.path.append("/home/sjb/sjb/make-a-stable-diffusion-video/src")
       ```

     - `from diffusers.pipelines.stable_diffusion.pipeline_stable_diffusion_video_inpaint import StableDiffusionVideoInpaintPipeline` : diffusers에서 StableDiffusionVideoInpaintPipeline를 가져오는데, 복잡하고, 오류가 많아 한 번에 경로를 지정해주었다.

       ```jsx
       ### 이전
       from diffusers import StableDiffusionVideoInpaintPipeline
       ```

       ```jsx
       ### 이후
       import sys
       sys.path.append("/home/sjb/sjb/make-a-stable-diffusion-video/src")
       from diffusers.pipelines.stable_diffusion.pipeline_stable_diffusion_video_inpaint import StableDiffusionVideoInpaintPipeline
       ```

     - make_a_stable_diffusion_video 아래의 init.py를 추가했다.

       - 파이썬에서 디렉토리를 패키지로 인식하려면 그 디렉토리에 `__init__.py` 파일이 있어야 한다. `__init__.py` 파일은 해당 디렉토리를 패키지로서 인식시키고, 모듈을 포함한 패키지의 초기화를 담당합니다. 만약 해당 디렉토리가 패키지로서 인식되지 않으면, 상대 경로로 모듈을 찾는 데에 문제가 발생할 수 있다.

   - 라이브러리 설치

     ```jsx
     pip install torch
     pip install transformers
     pip install xformers
     pip install imageio
     ```

   - 모델 다운 받는 중

     ![Untitled](/../../images/2023-08-04-[논문 조사] Audio로 Image 편집하는 논문 있는지 조사하기 (copy) (copy)/Untitled (9).png)

     - 메모리 부족 오류 발생

       ```bash
       torch.cuda.OutOfMemoryError: CUDA out of memory. Tried to allocate 2.81 GiB (GPU 0; 11.74 GiB total capacity; 7.54 GiB already allocated; 2.31 GiB free; 7.56 GiB reserved in total by PyTorch) If reserved memory is >> allocated memory try setting max_split_size_mb to avoid fragmentation.  See documentation for Memory Management and PYTORCH_CUDA_ALLOC_CONF
       ```

       - 총 11.74 GiB의 GPU 용량 중 이미 7.54 GiB가 할당되어 있거  2.31 GiB의 여유 메모리가 남아있지만, 현재 요청하려는 메모리는 추가 2.81 GiB이라 오류가 발생하였다.

         - ⇒ 실행 여부 확인이 목적이므로, 배치 크기를 줄여 GPU 메모리 사용량을 줄인다.

           - 하지만 배치 크기는 1이다.

             ![Untitled](/../../images/2023-08-04-[논문 조사] Audio로 Image 편집하는 논문 있는지 조사하기 (copy) (copy)/Untitled (10).png)

           - Readme를 다시 읽어보기 적어도 24gb의 VRAM이 필요하다고 한다. ⇒ 용량 부족으로 코드 실행 보류하기로 한다.

             ![Untitled](/../../images/2023-08-04-[논문 조사] Audio로 Image 편집하는 논문 있는지 조사하기 (copy) (copy)/Untitled (11).png)