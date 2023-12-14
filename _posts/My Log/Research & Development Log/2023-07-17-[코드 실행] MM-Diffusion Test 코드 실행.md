---
title:  "[코드 실행] MM-Diffusion Test 코드 실행"
categories: [My Log, Research & Development Log]
tags: [diffusion, log]
---

(이전 컴퓨터에서 실행한 내용이라 실행 결과 파일은 첨부하지 못하였다.)

### GPU 메모리 부족 문제 발생 (해결)

- 해결: NUM_GPUS=4 ⇒ NUM_GPUS=1 으로 수정했더니 코드가 실행된다.

  - multimodal_sample_sr.sh

  ```bash
  MODEL_FLAGS="--cross_attention_resolutions 2,4,8  --cross_attention_windows 1,4,8 
  --cross_attention_shift True  --video_attention_resolutions 2,4,8
  --audio_attention_resolutions -1
  --video_size 16,3,64,64 --audio_size 1,25600 --learn_sigma False --num_channels 128 
  --num_head_channels 64 --num_res_blocks 2 --resblock_updown True --use_fp16 True
  --use_scale_shift_norm True"
  
  SRMODEL_FLAGS="--sr_attention_resolutions 8,16,32  --large_size 256  
  --small_size 64 --sr_learn_sigma True 
  --sr_num_channels 192 --sr_num_heads 4 --sr_num_res_blocks 2 
  --sr_resblock_updown True --use_fp16 True --sr_use_scale_shift_norm True"
  
  # Modify --devices according your GPU number
  DIFFUSION_FLAGS="--diffusion_steps 1000 --noise_schedule linear
  --all_save_num 64 --save_type mp4  --devices 0,1
  --batch_size 4   --is_strict True --sample_fn dpm_solver"
  
  SR_DIFFUSION_FLAGS="--sr_diffusion_steps 1000  --sr_sample_fn ddim  --sr_timestep_respacing ddim25"
  
  # Modify the following paths to your own paths
  # MULTIMODAL_MODEL_PATH="/data10/rld/outputs/MM-Diffusion/models/AIST++.pt"
  # SR_MODEL_PATH="/data10/rld/outputs/MM-Diffusion/models/AIST++_SR.pt"
  # OUT_DIR="/data10/rld/outputs/MM-Diffusion/samples/multimodal-sample-sr/dpm_solver"
  # REF_PATH="/data10/rld/dataset/AIST++_crop/train"
  
  MULTIMODAL_MODEL_PATH="data/outputs/models/AIST++.pt"
  SR_MODEL_PATH="data/outputs/models/AIST++_SR.pt"
  OUT_DIR="data/outputs/samples/multimodal-sample-sr/dpm_solver"
  REF_PATH="data/dataset/AIST++_crop/train"
  
  NUM_GPUS=1
  mpiexec -n $NUM_GPUS python3 py_scripts/multimodal_sample_sr.py  \\
  $MODEL_FLAGS $SRMODEL_FLAGS $DIFFUSION_FLAGS $SR_DIFFUSION_FLAGS --ref_path ${REF_PATH} \\
  --output_dir ${OUT_DIR} --multimodal_model_path ${MULTIMODAL_MODEL_PATH}  --sr_model_path ${SR_MODEL_PATH}
  ```

  - GPU 메모리를 다 썼다고 에러가 뜬다

    ```
    torch.cuda.OutOfMemoryError: CUDA out of memory. Tried to allocate 6.00 GiB (GPU 0; 23.66 GiB total capacity; 13.74 GiB already allocated; 5.63 GiB free; 15.96 GiB reserved in total by PyTorch) If reserved memory is >> allocated memory try setting max_split_size_mb to avoid fragmentation.  See documentation for Memory Management and PYTORCH_CUDA_ALLOC_CONF
    ```

  - 방법1: 배치 사이즈를 줄인다. (O)

    - ```
      --batch_size
      ```

       를 4에서 2로 줄인다.

      - original video 8개, sr video 2개 만들고 멈춤

  - 방법2:  diffusion step 값을 줄인다. (X)

    - `--diffusion_steps` 를 1000에서 500으로 줄인다. ⇒ 효과 없음

### multimodal_sample_sr.sh 코드 수정

- Modify --devices according your GPU number

  - 수정 전

    ```bash
    # Modify --devices according your GPU number
    DIFFUSION_FLAGS="--diffusion_steps 1000 --noise_schedule linear
    --all_save_num 64 --save_type mp4  --devices 0,1,2,3
    --batch_size 4   --is_strict True --sample_fn dpm_solver"
    ```

  - 수정 후

    ```bash
    # Modify --devices according your GPU number
    DIFFUSION_FLAGS="--diffusion_steps 1000 --noise_schedule linear
    --all_save_num 64 --save_type mp4  --devices 0,1
    --batch_size 4   --is_strict True --sample_fn dpm_solver"
    ```

- Modify the following paths to your own paths

  - 수정 전

    ```bash
    # Modify the following paths to your own paths
    MULTIMODAL_MODEL_PATH="/data10/rld/outputs/MM-Diffusion/models/AIST++.pt"
    SR_MODEL_PATH="/data10/rld/outputs/MM-Diffusion/models/AIST++_SR.pt"
    OUT_DIR="/data10/rld/outputs/MM-Diffusion/samples/multimodal-sample-sr/dpm_solver"
    REF_PATH="/data10/rld/dataset/AIST++_crop/train"
    
    NUM_GPUS=4
    ```

  - 수정 후

    ```bash
    MULTIMODAL_MODEL_PATH="data/outputs/models/AIST++.pt"
    SR_MODEL_PATH="data/outputs/models/AIST++_SR.pt"
    OUT_DIR="data/outputs/samples/multimodal-sample-sr/dpm_solver"
    REF_PATH="data/dataset/AIST++_crop/train"
    
    NUM_GPUS=1
    ```

- 결과

  ```bash
  MODEL_FLAGS="--cross_attention_resolutions 2,4,8  --cross_attention_windows 1,4,8 
  --cross_attention_shift True  --video_attention_resolutions 2,4,8
  --audio_attention_resolutions -1
  --video_size 16,3,64,64 --audio_size 1,25600 --learn_sigma False --num_channels 128 
  --num_head_channels 64 --num_res_blocks 2 --resblock_updown True --use_fp16 True
  --use_scale_shift_norm True"
  
  SRMODEL_FLAGS="--sr_attention_resolutions 8,16,32  --large_size 256  
  --small_size 64 --sr_learn_sigma True 
  --sr_num_channels 192 --sr_num_heads 4 --sr_num_res_blocks 2 
  --sr_resblock_updown True --use_fp16 True --sr_use_scale_shift_norm True"
  
  # Modify --devices according your GPU number
  DIFFUSION_FLAGS="--diffusion_steps 1000 --noise_schedule linear
  --all_save_num 64 --save_type mp4  --devices 0,1
  --batch_size 4   --is_strict True --sample_fn dpm_solver"
  
  SR_DIFFUSION_FLAGS="--sr_diffusion_steps 1000  --sr_sample_fn ddim  --sr_timestep_respacing ddim25"
  
  # Modify the following paths to your own paths
  # MULTIMODAL_MODEL_PATH="/data10/rld/outputs/MM-Diffusion/models/AIST++.pt"
  # SR_MODEL_PATH="/data10/rld/outputs/MM-Diffusion/models/AIST++_SR.pt"
  # OUT_DIR="/data10/rld/outputs/MM-Diffusion/samples/multimodal-sample-sr/dpm_solver"
  # REF_PATH="/data10/rld/dataset/AIST++_crop/train"
  
  MULTIMODAL_MODEL_PATH="data/outputs/models/AIST++.pt"
  SR_MODEL_PATH="data/outputs/models/AIST++_SR.pt"
  OUT_DIR="data/outputs/samples/multimodal-sample-sr/dpm_solver"
  REF_PATH="data/dataset/AIST++_crop/train"
  
  NUM_GPUS=1
  mpiexec -n $NUM_GPUS python3 py_scripts/multimodal_sample_sr.py  \\
  $MODEL_FLAGS $SRMODEL_FLAGS $DIFFUSION_FLAGS $SR_DIFFUSION_FLAGS --ref_path ${REF_PATH} \\
  --output_dir ${OUT_DIR} --multimodal_model_path ${MULTIMODAL_MODEL_PATH}  --sr_model_path ${SR_MODEL_PATH}
  ```

### video2audio_sample.sh 코드 수정 & 실행

- 수정 전

  ```bash
  # if classifier_scale(λ) is 0, conditional generation follows the replacement based method
  # if classifier_scale(λ) is larger than 0, conditional generation follows the gradient based method
  DIFFUSION_FLAGS="--diffusion_steps 1000 --noise_schedule linear
  --all_save_num 48 --save_type mp4  --devices 7
  --batch_size 2   --is_strict True --sample_fn ddpm --classifier_scale 3.0"
  
  MODEL_PATH="/data10/rld/outputs/MM-Diffusion/models/AIST++.pt"
  OUT_DIR="/data10/rld/outputs/MM-Diffusion/video2audio/video2audio"
  REF_PATH="/data10/rld/data/AIST++_crop/train"
  NUM_GPUS=1
  ```

- 수정 후

  ```bash
  # if classifier_scale(λ) is 0, conditional generation follows the replacement based method
  # if classifier_scale(λ) is larger than 0, conditional generation follows the gradient based method
  DIFFUSION_FLAGS="--diffusion_steps 1000 --noise_schedule linear
  --all_save_num 48 --save_type mp4  --devices 2
  --batch_size 2   --is_strict True --sample_fn ddpm --classifier_scale 3.0"
  
  MODEL_PATH="data/outputs/models/AIST++.pt"
  OUT_DIR="data/outputs/video2audio/video2audio"
  REF_PATH="data/dataset/AIST++_crop/train"
  NUM_GPUS=1
  ```

- 결과

  ```bash
  MODEL_FLAGS="--cross_attention_resolutions 2,4,8  --cross_attention_windows 1,4,8 
  --cross_attention_shift True  --video_attention_resolutions 2,4,8
  --audio_attention_resolutions -1
  --video_size 16,3,64,64 --audio_size 1,25600 --learn_sigma False --num_channels 128 
  --num_head_channels 64 --num_res_blocks 2 --resblock_updown True --use_fp16 True
  --use_scale_shift_norm True"
  
  SRMODEL_FLAGS="--sr_attention_resolutions 8,16,32  --large_size 256  
  --small_size 64 --sr_learn_sigma True 
  --sr_num_channels 192 --sr_num_heads 4 --sr_num_res_blocks 2 
  --sr_resblock_updown True --use_fp16 True --sr_use_scale_shift_norm True"
  
  # Modify --devices according your GPU number
  DIFFUSION_FLAGS="--diffusion_steps 1000 --noise_schedule linear
  --all_save_num 64 --save_type mp4  --devices 0,1
  --batch_size 4   --is_strict True --sample_fn dpm_solver"
  
  SR_DIFFUSION_FLAGS="--sr_diffusion_steps 1000  --sr_sample_fn ddim  --sr_timestep_respacing ddim25"
  
  # Modify the following paths to your own paths
  # MULTIMODAL_MODEL_PATH="/data10/rld/outputs/MM-Diffusion/models/AIST++.pt"
  # SR_MODEL_PATH="/data10/rld/outputs/MM-Diffusion/models/AIST++_SR.pt"
  # OUT_DIR="/data10/rld/outputs/MM-Diffusion/samples/multimodal-sample-sr/dpm_solver"
  # REF_PATH="/data10/rld/dataset/AIST++_crop/train"
  
  MULTIMODAL_MODEL_PATH="data/outputs/models/AIST++.pt"
  SR_MODEL_PATH="data/outputs/models/AIST++_SR.pt"
  OUT_DIR="data/outputs/samples/multimodal-sample-sr/dpm_solver"
  REF_PATH="data/dataset/AIST++_crop/train"
  
  NUM_GPUS=1
  mpiexec -n $NUM_GPUS python3 py_scripts/multimodal_sample_sr.py  \\
  $MODEL_FLAGS $SRMODEL_FLAGS $DIFFUSION_FLAGS $SR_DIFFUSION_FLAGS --ref_path ${REF_PATH} \\
  --output_dir ${OUT_DIR} --multimodal_model_path ${MULTIMODAL_MODEL_PATH}  --sr_model_path ${SR_MODEL_PATH}
  ```