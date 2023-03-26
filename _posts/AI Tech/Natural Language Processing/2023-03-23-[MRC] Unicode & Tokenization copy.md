---
title:  "[MRC] Unicode & Tokenization"
# excerpt: "16~20년 코딩 테스트 기출문제 유형 분석"
categories: [AI Tech, Natural Language Processing]
tags: [Unicode, 인코딩, UTF-8, Tokenization, BPE]
---

전 세계 모든 문자를 다룰 수 있는 Unicode와 텍스트를 토큰 단위로 나누는 Tokenization에 대해 알아보자.  

<br><br>  

# Unicode & Tokenization

### Unicode란

전 세계의 모든 문자를 일관되게 표현하고 다룰 수 있도록 만들어진 문자set이다. 각 문자마다 숫자 하나에 매핑한다.

- U+AC00
    - “U+”: 유니코드를 뜻하는 접두어
    - “AC00”: 16진수 Code Point

### 인코딩과 UTF-8

- 인코딩이란?
    - 문자를 컴퓨터에서 저장 및 처리할 수 있게 이진 수로 바꾸는 것
- UTF-8(Unicode Transformation Format)이란?
    
    ![image](https://user-images.githubusercontent.com/89712324/227760005-35ccf7d0-d30d-4667-8944-7c2ab00dfac0.png){: width="500"}
    
    - 현재 가장 많이 쓰는 인코딩 방식이다.
    - 문자 타입에 따라 다른 길이의 바이트를 할당한다.
    - 1byte로는 표준 ASCII를 표현하며 영어를 모두 표현할 수 있다. 하지만 다른 언어들을 사용하려면 더 많은 byte가 필요하다.
    - 2byte는 Arabic, Hebrew, 대부분의 유럽 언어들을 표현할 수 있고,
    - 3byte로는 한글을 포함한 대부분 현대 글자를 모두 표현할 수 있다.
    - 4byte로는 이모지 등을 포함한 모든 유니코드 문자들을 표현할 수 있다.

### Python에서 Unicode 다루기

python으로 Unicode를 다루는 것은 꼭 갖춰야하는 매우 중요한 Skill이다. 

python3부터는 String 타입을 Unicode를 표준으로 사용하고 있다.

- ord()
    
    ```python
    # 알파벳은 1byte내로 표현하며, ASCII 결과와 같다.
    ord('A')
    >> 65
    hex(ord('A'))
    >> '0x41'
    
    # 한글은 3byte까지 가야하므로 숫자가 높다.
    ord('가')
    >> 44032
    hex(ord('가'))
    >> '0xac00'
    ```
    
- chr()
    
    ```python
    chr(44032)
    >> '가'
    chr(0xAC00)
    >> '가'
    
    chr(65)
    >> 'A'
    chr(0x41)
    >> 'A'
    ```
    

### Unicode와 한국어

한국어는 한자 다음으로 유니코드에서 많은 개수를 차지한다. 유니코드에서는 완성형, 조합형으로 한글을 표현한다.

- 완성형
    - 현대 한국어의 자모 조합으로 나타날 수 있는 모든 완성형 한글은 총 11,172자이다.
    - U+AC00 ~ U+D7A3 으로 표현할 수 있다.
- 조합형
    - 조합하여 글자를 만들 수 있는 초성, 중성, 종성을 표현한다.
    - U+1100 ~ U+11FF, U+A960 ~ U+A97F, U+D7B0 ~ U+D7FF 으로 표현할 수 있다.

```python
chr(0x1100)
>> 'ㄱ'

chr(0x1161)
>> 'ㅏ'

chr(0x1100) + chr(0x1161)
>> '가'

# 조합형
len('가')
>> 2

# 완성형
len('가')
>> 1 
```

### Tokenizing

Tokenizing이란 텍스트를 토큰 단위로 나누는 것이다.

단어(띄어쓰기 기준), 형태소, subword 등 여러 토큰 기준이 사용된다.

띄어쓰기 기준으로 한 tokenizing은 효율적이지 않다고 한다. 주로 형태소나 subword tokenizing을 사용한다고 한다.

### Subword Tokenizing

자주 쓰이는 글자 조합은 한 단위로 취급하고, 자주 쓰이지 않는 조합은 subword로 쪼갠다.

“##”은 디코딩을 할 때, 해당 토큰을 앞 토큰에 띄어쓰기 없이 붙인다는 것을 의미한다.

```python
from transformers import BertTokenizer
tokenizer = BertTokenizer.from_pretrained("bert-base-multilingual-cased")
tokenizer.tokenizer('아버지 가방에 들어가신다.')
>> ['아버지', '가', '##방', '##에', '들어', '##가', '##신', '##다']
```

Bert에 사용된 토크나이저를 사용했다. 이 토크나이저는 bert-base-multilingual-cased에 귀속된 방법 중 하나이다. 토크나이저는 모델의 일부라고 할 수 있다.

## BPE (Byte-Pair Encoding)

BPE는 데이터 압축용으로 제안된 알고리즘이며, NLP에서 토크나이징용으로 활발하게 사용되고 있다.

1. 가장 자주 나오는 글자 단위 Bigram (or Byte pair)를 다른 글자로 치환한다.
2. 치환된 글자를 저장해둔다.
3. 1~2번을 반복한다.
    
    ![image](https://user-images.githubusercontent.com/89712324/227760029-5f6b0352-0dd9-4215-bbfc-cc93f3e4af70.png){: width="200"}
    
    X는 aaab 즉, 글자 4개, Y와 Z는 각각 글자 2개를 뜻하는 것을 알 수 있다.
    

regular expression 같은 방식으로 사람이 직접 rule을 설정하여 토크나이징을 하였지만 이 방법에 한계가 있다. 따라서 BPE와 같은 Data Driven 방식을 일반적으로 많이 사용하고 있다.