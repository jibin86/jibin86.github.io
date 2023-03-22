---
title:  "[Hands-On ML] 11장 심층 신경망 훈련하기(6) - 규제 (L1, L2, dropout, max-norm)"
categories: [AI Tech, Hands-On ML]
tags: [Hands-On ML, 규제]
---
# 11.4 규제를 사용해 과대적합 피하기  

지금까지 규제 방법 중 하나인 조기 종료, 배치 정규화 등을 배웠다.  

이번 포스트에서는 L1, L2 규제, 드롭아웃(dropout), 맥스-노름(max-norm) 규제를 다뤄볼 것이다.  

심층 신경망은 수만 개, 수백만 개의 파라미터를 가지고 있어 자유도가 매우 높으며 복잡한 데이터셋을 학습할 수 있다. 하지만 자유도가 높으면 과대적합되기 쉬우므로 규제가 필요하다.



<br>
<br>

## 11.4.1 L<sub>1</sub>과 L<sub>2</sub> 규제  

가중치에 규제 강도 0.01을 사용하여 L2 규제를 적용해보자. (0.01이 default)



```python
# version1
import keras
layer = keras.layers.Dense(100, activation="elu",
                           kernel_initializer="he_normal",
                           kernel_regularizer=keras.regularizers.l2(0.01))
```


```python
# version2: functools.partial() 함수로 기본 매개변수 값을 사용하여 함수 호출을 감싼다.
from functools import partial

RegularizedDense = partial(keras.layers.Dense,
                           activation="elu",
                           kernel_initializer="he_normal",
                           kernel_regularizer=keras.regularizers.l2(0.01))

model = keras.models.Sequential([
    keras.layers.Flatten(input_shape=[28, 28]),
    RegularizedDense(300),
    RegularizedDense(100),
    RegularizedDense(10, activation='softmax',
                     kernel_initializer="glorot_uniform")
])
```
   

<br>
<br>


## 11.4.2 드롭아웃  

## 11.4.3 몬테 카를로 드롭아웃  

## 11.4.4 맥스-노름 규제  

# 11.5 요약 및 실용적인 가이드라인

