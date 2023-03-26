---
title:  "[MRC] 한국어 MRC 데이터셋 - KorQuAD Dataset"
# excerpt: "16~20년 코딩 테스트 기출문제 유형 분석"
categories: [AI Tech, Natural Language Processing]
tags: [KorQuAD, Huggingface datasets]
---

전 세계 모든 문자를 다룰 수 있는 Unicode와 텍스트를 토큰 단위로 나누는 Tokenization에 대해 알아보자.

<br><br>  

# Looking into the Dataset

KorQuAD 데이터셋 살펴보자

## KorQuAD

KorQuAD는 LG CNS가 AI 연구를 위해 공개한 질의응답/기계독해 한국어 데이터셋이다.

![image](https://user-images.githubusercontent.com/89712324/227760039-4da70dd5-e743-4ec9-9c91-416630d01714.png)

- 인공지능이 한국어 질문에 대한 답변을 하도록 학습하기 위한 데이터셋이다.
- 한국어 위키피디아 1550개의 문서에 대해서 하위 10649개의 문서들과 크라우드 소싱을 통해 제작한 63952개의 질의응답 쌍으로 구성되어있다. 
(** 크라우드 소싱: 기업활동의 전 과정에 소비자 또는 대중이 참여할 수 있도록 일부를 개방하고 참여자의 기여로 기업활동 능력이 향상되면 그 수익을 참여자와 공유하는 방법)
- 누구나 데이터를 내려받고, 학습한 모델을 제출하고 공개된 리더보드에 평가를 받을 수 있다.
→ 객관적인 기준을 가진 연구 결과 공유가 가능해졌다.
국내 다수의 회사, 학교에서 참여하고 있다.
- 현재 v1.0, v2.0이 공개되었다. 
2.0은 1.0보다 긴 분량의 문서가 포함되어 있으며, 단순 자연어 문장 뿐 아니라 복잡한 표와 리스트 등을 포함하는 HTML 형태로 표현되어 있어, 문서 전체 구조에 대한 이해가 필요하다.

![image](https://user-images.githubusercontent.com/89712324/227760070-204bc78d-87e8-4636-ad01-a074d65205b9.png)

## KorQuAD 데이터 수집 과정

- SQuAD v1.0의 데이터 수집 방식을 벤치마크하였다.
1. 대상 문서 수집
위키 백과에서 수집한 글들을 문단 단위로 정제, 이미지/URL 제거
짧은 문단, 수식이 포함된 문단 등 제거
2. 질문/답변 생성
크라우드 소싱을 통해 질의응답 70,000+쌍 생성
작업자가 양질의 질의응답 쌍을 생성하도록 상세한 가이드라인을 제시함
3. 2차 답변 태깅
앞에서 생성한 질문에 대해 사람이 직접 답해보면서 Human performance 측정
앞서 질문/답변 생성 과정에 참여한 사람은 참여 불가

## HuggingFace Datasets 라이브러리 소개

- HuggingFace에서 만든 Datasets는 자연어처리에 사용되는 대부분의 데이터셋과 평가 지표를 접근하고 공유할 수 있게끔 만든 라이브러리이다.
- Numpy, Pandas, PyTorch, Tensorflow2와 호환된다.
- 접근가능한 모든 데이터셋이 memory-mapped, cached 되어있어 데이터를 로드하면서 생기는 메모리 공간 부족이나 전처리 과정 반복의 번거로움 등을 피할 수 있다.
- KorQuAD 데이터셋의 경우 squad_kor_v1, squad_kor_v2로 불러올 수 있다.
HuggingFace의 Transformers라는 라이브러리를 설치한 후 아래 두 줄의 코드로 데이터를 로드할 수 있다.

```python
from datasets import load_dataset
dataset = load_dataset('squad_kor_v1', split='train')
```

![image](https://user-images.githubusercontent.com/89712324/227760082-d013a6ad-3030-48a9-9f3d-db2d0aecd022.png)

KorQuAD는 Extractive Answer Datasets이므로 질의(question)에 대한 답이 항상 주어진 지문(context)의 segment (or span)으로 존재한다.

answer_start: answer가 지문 내에서 몇 번째에 존재하는가를 알려준다. 답의 언급이 여러 개이지만 답변을 유추한 문장이 하나일 수 있다.

answer_start가 주어진 경우를 strong supervision, 그렇지 않은 경우를 distant supervision이라고 한다.

![image](https://user-images.githubusercontent.com/89712324/227760091-9db352a7-3f4d-453d-ba1a-09ded55aa032.png)

dataset을 print하면 딕셔너리 형태로 보여지게 된다.

answer가 여러 개 존재할 수 있기 때문에 Key ‘answers’의 Value는 리스트형이다. 하지만 대부분의 answer은 하나고, 실제로 학습할 때 answer은 하나만 쓰게 된다.

## KorQuAD 질문 유형

![image](https://user-images.githubusercontent.com/89712324/227760111-606f077f-7edb-4a89-9a24-11b6af823e74.png)

- 절반 이상이 구문 변형 질문이며, 이 부분은 Paraphrasing 문제에 속한다.

## KorQuAD 답변 유형

![image](https://user-images.githubusercontent.com/89712324/227760122-2e0ddf01-f4ba-439c-89fb-540f4defb2ae.png){: width="500"}

원인, 방법은 까다로운 문제이지만 차지하는 비율이 낮고, 대상, 인물, 시간, 장소 등 구체화할 수 있고 정의하기 쉬운 답변이 대부분이다.