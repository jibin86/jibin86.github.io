---
title:  "[논문 리뷰] Model soups: averaging weights of multiple fine-tuned models improves accuracy without increasing inference time"
categories: [AI Tech, Computer Vision, Paper Review]
tags: [model soup]
---   

fine-tuning된 모델의 parameter를 평균낸 single model 방식을 제안하는 Model soups 논문 리뷰를 해보았다.  
<br>  

# Abstract

일반적으로 pretrained model들을 테스트해보고 결과가 좋은 모델을 선정해 fine-tuning하여 transfer learning하는 방식을 사용한다.

하지만 이러한 방식에는 몇 가지 문제가 존재한다.

첫 번째로는 선정된 single model로는 전체적으로 높은 성능을 내기가 어렵다. 따라서 여러 모델을 ensemble을 하여 성능을 높이는 방법을 사용하지만 매우 높은 inference cost가 발생하기 때문에 이 방법 또한 현실적으로는 어렵다.

또 다른 문제로는 fine-tuning된 모델이 downstream task를 수행했을 때 Out-of-distribution performance가 줄어든다. Out-of-distribution performance는 모델이 학습되지 않은 데이터를 처리할 때의 모델의 성능을 말한다.

따라서 이 논문에서는 추가적인 inference나 cost없이 모델의 parameter를 평균낸 single model 방식을 제안하여 위와 같은 문제들을 해결하고자 한다.  
<br>  


# Related Work

## (1)Averaging model weights

### Stochastic Weight Averaging(2018)

![image](https://user-images.githubusercontent.com/89712324/227086423-6b995220-c52c-4259-84aa-c1bcf95147a9.png){: width="300"}

Stochastic Weight Averaging(2018) 연구를 살펴보면 서로 다른 하이퍼파라미터로 학습한 모델들의 weight들을 평균 내면, 이는 loss landscape에서 가장 낮은 값을 갖는 값으로 향하며 더 좋은 성능을 낼 수 있다는 것을 알 수 있다.  
<br>  


### Neyshabur et al, 2021

Neyshabur et al, 2021 연구에서는 같은 intialization에서 시작하여 독립적으로 fine-tuning된 모델은 동일한 landscape으로 향한다는 것을 발견하였다.   
<br>  


### Wortsman et al.

Wortsman et al.에서는 하나의 zero-shot CLIP 모델과 fine-tuning된 모델 사이의 Weight 평균을 내면 더 좋은 모델이 나올 수 있으며 Out-of-distribution 성능 향상도 된다는 결과를 보였다.

![image](https://user-images.githubusercontent.com/89712324/227086445-0bc2c2c8-e475-4424-b138-4575f853503c.png)

위 그림에서 별 모양 기호 θ0는 pretrained된 모델이며, 마름모와 정사각형, 원 기호는 서로 다른 하이퍼파라미터(learning_rate, seed)로 학습된 모델이다.

위 그림을 보면 서로 다른 하이퍼파라미터로 평균을 냈을 때 개별 모델보다 최적점에 가까워지며 더 좋은 성능을 낼 수 있다는 것을 알 수 있다.  
<br>  


각도의 관점에서 Error landscape visualizations를 바라보자.

![image](https://user-images.githubusercontent.com/89712324/227086470-a25a683c-46c9-43b5-b46c-1fb71ce0ab1a.png){: width="400"}

하이퍼파라미터 seed와 learning_rate, augmentation을 다르게 학습한 두가지 모델의 사이의 각도를 측정한다. x축은 모델 간의 각도이며, y축은 Accuracy gain 이다 ⇒ (θ1과 θ2의 weight을 평균낸 성능) + (각θ1과 θ2의 성능을 평균낸 결과).

위 그래프를 통해 모델 간의 각도 90도에 가까워질 수록 더 좋은 성능을 낸다는 것을 알 수 있다.  
<br>  


## (2) Pre-training and fine-tuning (weight aggregation)

### Shu et al.

adaptive aggregation 학습을 통해 여러 pretrained model들을 하나의 single model로 만들어 transfer learning 성능을 개선하려고 노력하였다.

![image](https://user-images.githubusercontent.com/89712324/227086488-190c2cba-a239-4e09-8ff8-e56d4eab2fa2.png){: width="600"}
<br>  


## (3) Ensembles

Ovadia et al. (Neurips 2019) 논문에서 Ensembles을 적용하면 distribution shift가 있어도 높은 정확도를 나타낸다는 것을 보여주었고, Gontijo-Lopes et al. 논문에서는 다양한 학습 방식들을 적용할 수록, 다양한 모델들을 앙상블 할 수록 더 좋은 성능을 나타낸다는 것을 보였다.  
<br>  


# Approach

Model soup을 만드는 3가지 방법이 있다.

1. Uniform soup
2. Greedy soup
3. Learned soup

논문에서는 Greedy soup를 중점적으로 다뤘다.

<br>  


## Uniform soup

Uniform soup은 단순하게 k개의 모델을 학습하고, 더해서 평균을 낸 모델이다. 단점으로는 모델을 선별하지 않고 모두 사용했기 때문에 성능이 낮은 모델도 포함이 되어 전체적으로 성능이 낮아질 수 있다.  
<br>  


## Greedy soup

![image](https://user-images.githubusercontent.com/89712324/227086696-fa8f353b-dc87-4fed-bbfd-34927f2d37fc.png){: width="400"}

Greedy soup는 Uniform soup와 다른 방식으로 모델을 선별한다.

Greedy soup은 validation accuracy를 기준으로 k개의 모델을 정렬을 한 뒤, 모델을 하나씩 포함하였을 때 accuracy가 향상되면 선택하고, 향상되지 않으면 제거하는 방식으로 모델을 선정한다. 이후 선정된 모델들의 weight을 평균내어 single 모델을 만든다.  
<br>  


## Learned soup

Learned soup은 Gradient 기반의 미니배치 최적화를 통해서 모델을 선정하는 방식이다. 하지만 Learned soup 방식을 진행하려면 한번에 50개 정도 되는 모델을 메모리에 올려야 하는데 현실적으로 어렵기 때문에 실용적이지 않다고 한다.  
<br>  


![image](https://user-images.githubusercontent.com/89712324/227090033-aaa3e49f-21ce-420e-82ea-8fd635426723.png)

위 Table을 통해서 논문에서 제시한 방식인 Soup: averaging weights of multiple fine-tuned models은 하나의 모델을 선정했을 때보다, 그리고 여러 모델을 ensemble했을 때보다 적은 cost으로 좋은 성능을 낼 수 있다는 것을 알 수 있다.  
<br>  


# Experiments

## 1. Fine-tuning CLIP (ViT-B/32) and ALIGN (EfficientNet-L2)

Pretrained된 CLIP, ALIGN 모델을 ImageNet으로 fine-tuning을 하였다.   
<br>  


### CLIP

CLIP에서는 random hyperparameter search를 통해 learning_rate, decay, training epochs, label smoothing, data augmentation 하이퍼파라미터를 설정하여 총 72개이 fine-tuned models를 얻었다.   
<br>  


### ALIGN

ALIGN에서는 grid search를 통해 learning_rate, data augmentation, mixup 하이퍼파라미터를 설정하여 총 12개의 fine-tuned models를 얻었다.   
<br>  


### Greedy Soup

앞서 Greedy Soup 방식을 정리했던 것처럼 모델들을 validation accuracy가 높은 순으로 정렬하고, 모델을 하나씩 추가하면서 accuracy가 떨어지지 않는 모델들을 선정한다. CLIP과 ALIGN 모두 5개의 모델을 선정하였다.

![image](https://user-images.githubusercontent.com/89712324/227090100-2e11d22a-2d60-4056-ba8b-eb9d01436515.png)

초록색 마름모 기호는 다양한 하이퍼파라미터로 개별적으로 학습한 모델의 accuracy를 나타내고, 보라색 별 모양 기호는 Greedy Soup의 accuracy를 나타낸다.

![image](https://user-images.githubusercontent.com/89712324/227086991-aa29a081-52c5-42f1-a582-cef0c89d358e.png){: width="400"}

위 결과를 통해 ImageNet과 the out-of-distribution에서 Greedy Soup 방식으로 학습한 모델이, 개별 모델 중 성능이 가장 높은 모델보다 더 좋은 성능을 나타낸다는 것을 알 수 있다.  
<br>  


## 2. Fine-tuning JFT-3B (ViT-G/14)

JFT-3B에서는 learning_rate, decay schedule, loss function, minimum crop size 등 다양한 하이퍼파라미터를 설정하여 총 58개의 fine-tuned models를 얻었다. 그 중 Greedy soup에서 14개의 모델 선정하였다.

![image](https://user-images.githubusercontent.com/89712324/227087030-bcfa96d9-05a3-4314-90ad-1fbb513c0e98.png)

ImageNet, Distribution shifts 데이터셋으로 성능을 측정하였을 때, ObjectNet을 제외한 모든 데이터셋에서 Greedy soup와 best individually fine-tuned model이 통계적으로 유의미한 성능 차이를 보인다는 것을 알 수 있었다. 즉, 대부분의 데이터셋에서 Greedy soup이 좋은 성능을 낸다는 것을 알 수 있다.  
<br>  


## 3. Fine-tuning Text Classification

![image](https://user-images.githubusercontent.com/89712324/227087084-9fd2c000-f6d4-4468-a6fd-e8a2ee8e9c1f.png)

Pretrained BERT와 T5 모델을 MRPC, RTE, CoLA, SST-2, 총 4가지 task에 대해서 fine-tuning을 하였다. 두 모델과 각각의 Task에서 Greedy soup이 Best individual model보다 높은 성능을 가진다는 것을 알 수 있었다.  
<br>  


# Analytically comparing soups to ensembles

soup을 구성하는 두 모델이 있다고 해보자. soup 모델의 error는 개별 모델 error의 최솟값보다 작다고 할 수는 없다. 하지만 일반적으로 (많은 문헌을 바탕으로) ensemble의 error는 개별 모델 error의 최솟값보다 작다고 알려져 있다. 따라서 ensemble error와 soups error가 유사하다면 soups model이 두 개별 모델의 성능을 능가한다는 것을 알 수 있다.  
<br>  


# Limitation

## Applicability

거대한 데이터셋에서 pretrained된 모델을 fine-tuning하여 greedy soup을 적용한 것에 비해, 작은 데이터셋에서 pretrained된 model을 fine-tuning한 모델에 greedy soup을 적용한 것의 성능 향상이 크지 않았다.  
<br>  


## Calibration

Ensembles 모델은 각 모델의 결과를 앙상블하기 때문에 모델의 예측이 틀렸을 가능성에 대해 알 수 있지만, Soup 모델은 예측 이전에 weight를 평균내기 때문에 모델의 예측이 틀렸을 가능성에 대해 알지 못하는 한계가 있다.  
<br>  


# Conclusions

Soup 모델은 fine-tuning 모델을 선정하는 방식에 Inference 동안에 추가적인 compute cost 없이, averaging weights하는 새로운 방식을 제시하였다.