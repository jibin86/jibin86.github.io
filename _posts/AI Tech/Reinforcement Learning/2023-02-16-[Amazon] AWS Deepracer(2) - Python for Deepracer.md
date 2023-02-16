---
title:  "[Amazon] AWS Deepracer(2) - Python for Deepracer"
categories: [AI Tech, Reinforcement Learning]
tags: [Deepracer]
---

이 포스트는 [AWS Deepracer 강의](https://dongorae.github.io/aws-deepracer-school/Basic/AWS_Deepracer_Overview/09_Deepracer_%EC%BD%98%EC%86%94_%EB%91%98%EB%9F%AC%EB%B3%B4%EA%B8%B0.html)를 바탕으로 공부한 내용을 정리하였습니다.
<br>

# Python for Deepracer

## Math Library

### **원주율(pi)과 자연상수(e)**

```python
# 라이브러리 가져오기
import math

# 원주율
print(math.pi)
>> 3.141592653589793

# 오일러 자연상수
print(math.e)
>> 2.718281828459045
```

### **각도 단위변환**

- math.degrees(라디안각) : 라디안각을 60분법각으로 변환
- math.radians(60분법각) : 60분법각을 라디안각으로 변환

```python
# 라디안각을 60분법각으로 변환
print(math.degrees(math.pi))
>> 180.0

# 60분법각을 라디안각으로 변환
print(math.radians(180))
>> 3.141592653589793
```


### **atan 함수**

- math.atan(기울기) : 기율기의 라디안각을 반환
- math.atan2(y성분, x성분) : 벡터의 라디안각을 반환

```python
rad = math.atan(1) # 기울기 1의 라디안각
deg = math.degrees(rad) # 라디안각을 60분법으로 변환
print(deg)
>> 45.0

x = 1
y = 3 ** (1/2)  # 루트 3
rad = math.atan2(y,x) # 벡터의 라디안각
deg = math.degrees(rad) # 라디안각을 60분법으로 변환
print(deg)
>> 59.99999999999999
```


### **삼각함수 sin,cos,tan**

- math.sin() : 사인함수
- math.cos() : 코사인함수
- math.tan() : 탄젠트 함수

```python
# cos 함수
x = math.cos(math.pi)
print(x)
>> -1.0

# sin 함수
x = math.sin(math.pi)
print(x)
>> 1.2246467991473532e-16
```


### **로그함수**

- math.log() : 자연로그
- math.log2() : 밑이 2인 로그
- math.log10() : 밑이 10인 로그

```python
# 자연로그
math.log(math.e)
>> 1.0
```

<br>

## 딥레이서의 보상함수 구조

- 시뮬레이션 환경으로부터 받아온 정보가 params에 딕셔너리로 저장되어있다.
- 보상함수는 params 변수를 입력 받아 보상을 출력하는 구조이다.
    
    ```python
    # 시뮬레이션 환경에서 받아온 정보
    params = {
        'x' : 10, # 차량의 x 좌표
    		'y' : 20, # 차량의 y 좌표
    		'speed' : 3.5, # 차량의 속도
    		'steering_angle' : 12.7, # 모든 바퀴가 트랙 내부에 위치하는지 여부
    		'all_wheels_on_track' :True,
    		# 기타 등등
    }
    ```
    
- 속도에 따른 보상함수 예시
    
    ```python
    # 속도가 클 수록 좋음
    def reward_function(params) :
        speed = params['speed']
        if speed > 2 :
            reward = 2    # 속도가 2보다 크면 보상 2
        elif speed > 1 :
            reward = 1    # 속도가 1보다 크고 2 이하면 보상 1
        else :
            reward = 0   # 속도가 1 이하면 보상 0
    
        return float(reward)   # 보상을 실수형으로 변환하여 리턴  
    
    reward = reward_function(params)
    print(reward)
    ```
