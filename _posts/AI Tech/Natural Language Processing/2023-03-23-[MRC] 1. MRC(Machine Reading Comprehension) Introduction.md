---
title:  "[MRC] MRC(Machine Reading Comprehension) Introduction"
# excerpt: "16~20년 코딩 테스트 기출문제 유형 분석"
categories: [AI Tech, Natural Language Processing]
tags: [MRC, Exact Match, ROUGE-L
, BLEU]
---

주어진 지문을 이해하고 주어진 질의의 답변을 추론하는 기계독해(MRC)와 MRC의 종류, MRC의 평가방법에 대해 알아보자.

<br><br>  

# Introduction to MRC

## Machine Reading Comprehension (MRC)의 개념

기계 독해(Machine Reading Comprehension: MRC)

: 주어진 지문(Context)을 이해하고, 주어진 질의(Query/Question)의 답변(Answer)을 추론하는 문제이다.

![image](https://user-images.githubusercontent.com/89712324/227759737-98d1393d-5b41-4d85-8ecc-3c2023343620.png){: width="500"}

질문이 들어오면 지문 내에서 답을 찾아 답변할 수 있는 모델을 만들 예정이다.

## MRC의 종류

### 1. Extractive Answer Datasets

: 질의(question)에 대한 답이 항상 주어진 지문(context)의 segment (or span)으로 존재한다.

- Question Anwsering Dataset
    - SQuAD, KorQuAD, NewsQA, Natural Questions, etc
        
        ![image](https://user-images.githubusercontent.com/89712324/227759770-754a5d9e-b289-4f32-ad21-d9ae9660f2c8.png){: width="500"}
        

### 2. Descriptive/Narrative Answering Datasets

: 답이 지문 내에 추출한 span이 아니라, 질의를 보고 생성된 sentence (or free-form)의 형태

- Question Anwsering Dataset
    - MS MARCO, Narrative QA
        
        ![image](https://user-images.githubusercontent.com/89712324/227759785-d40843fa-952e-4267-a727-b280953ff913.png){: width="500"}
        
        Answer가 위에 있는 지문 내에 존재하지 않는 자유로운 형태임을 알 수 있다.
        

### 3. Multiple-choice Answering Datasets

: 질의에 대한 답을 여러 개의 answer candidates 중 하나로 고르는 형태

- Question Anwsering Dataset
    - MCTest, RACE, ARC, etc
        
        ![image](https://user-images.githubusercontent.com/89712324/227759797-80ed371c-62ba-4e2d-803e-96326c5858db.png){: width="500"}
        

## Challenges in MRC

1. 단어들의 구성이 유사하지는 않지만 동일한 의미의 문장을 이해해야한다. 
    - 즉, 같은 의미지만 다른 말로 바꾸어 표현한 말, 동일 지시어을 이해해야한다.
    - ex) DuoRC, QuoRef
        
        ![image](https://user-images.githubusercontent.com/89712324/227759809-291e51a8-dfca-42f5-a08f-e7a9424486d3.png){: width="500"}
        
2. 지문 내에 답변이 존재하지 않을 수 있다. 그럴 때는 모르겠다 라는 말을 해야한다.
    - ex) SQuAD 2.0
        
        ![image](https://user-images.githubusercontent.com/89712324/227759817-94177f10-3a3e-40ff-9a9d-a2299e529e89.png){: width="300"}
        
3. Multi-hop reasoning
    - 여러 개의 document에서 질의에 대한 supporting fact를 찾아야지만 답을 찾을 수 있다.
    - ex) HotpotQA, QAngaroo
        
        ![image](https://user-images.githubusercontent.com/89712324/227759830-0eeaa996-4671-4448-8638-aa28812e1c29.png){: width="400"}
        

## MRC의 평가방법

### 1. Exact Match / F1 Score

위 두 가지 평가 방법은 Extractive Answer와 Multiple-choice Answering에 많이 사용된다.

- Exact Match / Accuracy
    - 예측한 답과 ground-truth가 정확히 일치하는 샘플의 비율이다. 전체 샘플 개수 중에서 일치한 샘플 개수의 비율
    - 일치하면 1점, 일치하지 않으면 0점을 준다.
    - 단점: 답변이 조금만 달라도 점수를 받지 못하는 문제가 생긴다. ⇒ F1 Score로 해결
- F1 Score
    - 예측한 답과 ground-truth 사이의 (단어)token overlap을 F1으로 계산
    
    ![image](https://user-images.githubusercontent.com/89712324/227759836-3ec1ed9e-9359-4023-96e0-6bc0ee816676.png)
    
    Precision: 불필요하게 예측값이 길어지면 하락한다.
    
    - TP/(TP+FP): **모델이 True라고 분류한 것 중에서** 실제 True인 것의 비율 (모델 True가 분모)
    
    Recall(): 실제 True인 것을 모델이 못 반영하면, 두 token들 중 겹치는 token이 작으면 하락한다.
    
    - TP/(TP+FN): **실제 True인 것 중에서** 모델이 True라고 예측한 것의 비율 (실제 True가 분모)
    
    ![image](https://user-images.githubusercontent.com/89712324/227759848-b2b102e0-0658-487e-9b09-a7c293d2ab21.png){: width="400"}
    
    - Exact Match 방식으로 답변이 정확히 일치하지 않으므로 0점을 주고, F1 Score 방식에서는 “5 days”가 overlap되므로 0.8점을 준다.

하지만 위의 방법을 Descriptive/Narrative Answering에 적용하기는 어렵다. ⇒ ROUGE-L / BLEU 평가 방식을 적용한다.

### 2. ROUGE-L / BLEU

위 두 가지 평가 방법은 Descriptive/Narrative Answering에 많이 사용된다.

- ROUGE-L (L⇒ Longest common subsequence)
    - 예측한 값과 ground-truth 사이의 recall(재현율)을 Longest common subsequence길이가 얼마인지를 계산한다.
    정답 길이 중에 모델이 낸 답과 겹치는 길이
- BLEU (Bilingual Evaluation Understudy)
    - 예측한 값과 ground-truth 사이의 precision(정밀도)을 N gram끼리 겹치는 비율을 계산한다.
    모델이 예측한 답 중에 정답과 겹치는 답 개수

![image](https://user-images.githubusercontent.com/89712324/227759856-1d6bcbf9-5c2c-4645-be5f-2d3390860441.png){: width="500"}