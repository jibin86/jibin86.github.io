---
title:  "[MRC] Extraction-based MRC"
# excerpt: "16~20년 코딩 테스트 기출문제 유형 분석"
categories: [AI Tech, Natural Language Processing]
tags: [Extraction-based MRC, attention mask, start postion/ end position]
---

지난 포스트에서 MRC의 개념과 종류, 평가방법에 대해서 다뤄봤다. 이번 포스트에서는 질문과 답변이 주어진 지문 내에 존재하는 Extraction-based MRC에 대해 자세히 알아볼 것이다. 
<br><br>

# Extraction-based MRC

## Extraction-based MRC

이전 포스트에서 Extraction-based MRC와 평가 방법에 대해서 다뤘기에 간단히 언급하고 넘어가겠다.

Extraction-based MRC는 질문과 답변이 항상 주어진 지문 내에 존재하는 Question Answering 방식이다.

텍스트의 위치만 파악하는 것으로 문제를 구성할 수 있다.

관련 데이터셋으로는 SQuAD, KorQuAD, NewsQA, Natural Questions 등이 있다. 위 데이터셋은 Hugging Face을 통해 다운받을 수 있다.

### Extraction-based MRC 평가 방법

1. EM: 예측값과 정답이 정확하게 일치하는 경우에만 1점을 부여하고, 그렇지 않으면 0점을 부여한다.
2. F1: 예측값과 정답의 overlap을 비율로 계산한다. 0~1점의 부분점수를 받을 수 있다.
    
    ![image](https://user-images.githubusercontent.com/89712324/227760169-e46c2b14-568c-465c-bc4e-c6bf36035224.png)
    
    - Precision ⇒ 겹치는 개수 / 모델이 예측한 단어 개수
    - Recall ⇒ 겹치는 개수 / 정답의 단어 개수
    - F1 ⇒ 2 * Precision / (Precision + Recall )
    - Ground-Truth가 여러 개일 때는 F1을 각각 구한 후 가장 높은 score를 택한다.
        
        ![image](https://user-images.githubusercontent.com/89712324/227760178-708c19bc-f712-4bf6-879a-2611f3f020c2.png)
        

왼쪽은 SQuAD 리더보드이며, 오른쪽은 KorQuAD의 리더보드이다.

![image](https://user-images.githubusercontent.com/89712324/227760191-333d6472-94eb-4eba-8b30-849ff66ca503.png)

### Extraction-based MRC Overview

![image](https://user-images.githubusercontent.com/89712324/227760223-5df31e4c-02a2-487c-b04d-52796b4b45b9.png)

Context Raw Data와 Question Raw Data를 토크나이징한다. 

그 다음 Word Embedding을 통해 토큰들을 벡터화하여 모델의 입력 값으로 넣는다. 이후 모델은 시작점과 끝점을 반환한다.

## Pre-processing

### Tokenization

Tokenization 텍스트를 작은 단위(Token)로 나누는 것이다.

띄어쓰기 기준, 형태소, subword 등 여러 단위 토큰 기준이 사용된다.

최근엔 OOV(Out-Of-Vocabulary)문제를 해결해주며, 정보학적으로 이점을 가진 BPE(Byte Pair Encoding)을 주로 사용한다.

우리는 BPE 방법론 중 하나인 WordPiece Tokenizer를 사용하려고 한다.

다음은 WorkPiece Tokenizer 사용 예시이다.

“미국 군대 내 두번째로 높은 직위는 무엇인가?”

⇒ [’미국’, ‘군대’, ‘내’, ‘두번째’, ‘##로’, ‘높은’ ‘직’, ‘##위는’, ‘무엇인가’, ‘?’]

### Special Tokens: [CLS], [SEP]

Question과 Context를 구분하기 위한 토큰이다. 스페셜 토큰들은 토크나이징 이후에도 토큰 형태로 들어간다.

![image](https://user-images.githubusercontent.com/89712324/227760239-b85d538e-403c-48ec-b6bd-4bb413edc708.png)

### Attention Mask

입력 시퀀스 중에서 attention을 연산할 때 무시할 토큰을 표시한다.

0은 무시하고, 1은 연산에 포함한다.

[PAD]는 문장의 길이를 똑같이 맞춰주기 위해 사용되는 토큰인데, 보통 [PAD]와 같은 의미가 없는 특수 토큰을 무시하기 위해 사용된다.

![image](https://user-images.githubusercontent.com/89712324/227760244-acbf4de2-3ceb-49c0-9e13-dc7b2f3c2433.png)

모델에 입력값과 attention mask을 같이 주면서, 무시할 토큰을 알려준다.

### Token Type IDs

질문 & 지문과 같이 입력이 2개 이상의 시퀀스일 때, 각각에게 ID를 부여하여 모델이 구분해서 해석할 수 있도록 유도한다.

예를 들어 질문에 해당하는 토큰은 0을 부여하고, 지문에 해당하는 토큰은 1을 부여한다. 그리고 PAD 토큰은 편의상 0을 둔다.

### 모델 출력값

Extraction-based에서는 답안을 생성하기보다는 시작 위치와 끝 위치를 예측하도록 학습한다. 즉 token classification 문제로 치환된다.

![image](https://user-images.githubusercontent.com/89712324/227760254-ada3186c-9024-4a6e-83a5-672c5492e484.png)

## Fine-tuning

모델의 Input값은 context, question의 토큰들이 임베딩된 값이며, Output값은 context, question의 contextualized된 임베딩 값이다. 

MRC에 적용하기 위해서는 output 값, 즉 context의 임베딩된 output값을 점수화 시켜야한다.

점수화하는 간단한 방법 중 하나는 지문 내에서 정답에 해당되는 각 임베딩을 linear transformation을 통해서 단어마다 하나의 숫자가 나올 수 있도록 바꿔주는 것이다. (이때 linear transformation도 학습해야된다.) 이후 모델의 **각 context 토큰마다 하나의 점수 output이 나오게 되며 점수가 높을 수록 정답 확률이 높다는 것을 의미**한다.

실제로 학습할 때는 linear transformation보다 softmax layer를 추가하여 cross-entropy loss로 학습하는 방식을 택한다.

따라서 출력된 start token position과 실제 답의 start token position을 cross-entropy loss를 통해 최적화한다. end token도 같은 방식으로 진행한다.

![image](https://user-images.githubusercontent.com/89712324/227760265-04bbb4a2-cdc9-40ec-90f8-1033d8a48ccf.png)

- 참고로 softmax는 벡터를 확률분포로 바꿔주는 함수이며, logit은 확률분포를 다시 벡터로 바꿔주는 함수이다. 즉 logit은 softmax의 역함수이다. softmax 함수는 로짓(logit) 벡터를 입력으로 받아서, 각 클래스에 대한 확률값을 출력한다. 로짓은 일반적으로 모델의 출력값을 말하며, 소프트맥스 함수의 입력으로 사용된다.
cross-entropy loss는 모델의 예측값과 실제 레이블 간의 차이를 측정하는 함수이다. 이 함수는 모델의 예측값(p_i)과 실제 레이블(y_i)을 입력으로 받아서, 이들 간의 차이를 측정하는데, 이 때, 모델의 예측값은 일반적으로 softmax 함수를 거쳐서 확률값으로 변환된 값을 사용한다.
    
    ```python
    cross_entropy_loss = -sum(y_i * log(p_i))
    # y_i는 실제 분포를 표현하는 one-hot 벡터의 i번째 원소
    # p_i는 모델이 예측한 확률 분포의 i번째 원소
    ```
    

## Post-processing

### 불가능한 답 제거하기

다음과 같은 경우는 불가능하므로 제거한다.

- End position이 Start position보다 앞에 있는 경우 (ex. start=90, end=80)
- 예측한 위치가 context를 벗어난 경우 (ex. question 위치 쪽에 답이 나온 경우)
- 미리 설정한 max_answer_length보다 길이가 더 긴 경우

### 최적의 답안 찾기

다음과 같은 순서대로 최적의 답안을 찾는다.

1. 예측된 Start, End position에서 score(logits)가 가장 높은 N개를 각각 찾는다.
2. 불가능한 start, end 조합을 제거한다.
3. 가능한 조합들을 score의 합이 큰 순서대로 정렬한다.
4. score가 가장 큰 조합을 최종 예측으로 선정한다.
5. Top-k가 필요한 경우 차례대로 내보낸다.
