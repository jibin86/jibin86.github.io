---
title:  "[Amazon] AWS Deepracer (2) Reward Function, Log Analysis"
categories: [AI Tech, Reinforcement Learning]
tags: [Deepracer]
---

이 포스트는 [AWS Deepracer 강의](https://dongorae.github.io/aws-deepracer-school/Basic/AWS_Deepracer_Overview/09_Deepracer_%EC%BD%98%EC%86%94_%EB%91%98%EB%9F%AC%EB%B3%B4%EA%B8%B0.html)를 바탕으로 공부한 내용을 정리하였습니다.
<br>

# Python for Deepracer

## Math Library
<br>


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

<br>

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

<br>

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

<br>

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

<br>

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
    
<br>


# Reward Function

## 기본 구조

- 입력: 딕셔너리 객체
    - 시뮬레이션에서 일어나는 상태에 대한 정보가 담겨있다.
        
        ```python
        def reward_function(params):
        	reward = ...
        	return float(reward)
        ```
        
- 보상함수 예시
    
    ```python
    import math
    
    def reward_function(params):
    # 1. 생존 보상 reward_survived 
        ########## [reward_survived] survived ########## 
        reward_survived = 1
        
    # 2. 트랙 위 안착 보상 reward_wheels 
         ########## [reward_wheels] all_wheels_on_track ########## 
        all_wheels_on_track = params['all_wheels_on_track']
        if all_wheels_on_track :
            reward_wheels = 1 
        else:
            reward_wheels = 0
        
    # 3. 속도 보상 reward_speed 
        ############## [reward_speed] speed #############
        speed = params['speed']
        reward_speed = speed / 4 
        
    # 4. 방향 보상 reward_direction 
        ############## [reward_direction] direction_diff #############
        waypoints = params['waypoints'] # 경로 좌표
        closest_waypoints = params['closest_waypoints']
        heading = params['heading']
        
        # 현재 에이전트와 가장 가까운 waypoint의 위치를 가져옵니다.
        next_point = waypoints[closest_waypoints[1]]
        prev_point = waypoints[closest_waypoints[0]]
    
        # track_direction 을 계산 
        track_direction = math.atan2(next_point[1] - prev_point[1], next_point[0] - prev_point[0])
        track_direction = math.degrees(track_direction)  
    
    		# 각의 차이 (direction_diff) 계산
        direction_diff = abs(track_direction - heading)
        if direction_diff > 180:
            direction_diff = 360 - direction_diff
        
        # 보상 결정
        direction_diff_rad = math.radians(direction_diff)
        reward_direction = math.cos(direction_diff_rad)
    
    # 4. 방향 보상 다른 버전
        x = params['x']
        y = params['y']
        
        steering_angle = params['steering_angle']
        steering_angle_rad = math.radians(steering_angle)
        
        heading = params['heading']
        heading_rad = math.radians(heading)
        
        closest_waypoints = params['closest_waypoints']
        waypoints = params['waypoints']
        waypoints_len = len(waypoints) - 1
        
        # 타켓 웨이포인트 좌표 구하기
        closest_previous, closest_next = closest_waypoints
        target_waypoint_idx = (closest_next + 4) % waypoints_len
        target_waypoint = waypoints[target_waypoint_idx]
        x_target, y_target = target_waypoint
        
        # 현재 위치에서 타켓 웨이포인트로의 벡터의 라디안 각 구하기
        x_target_vector = x_target - x
        y_target_vector = y_target - y
        target_rad = math.atan2(y_target_vector, x_target_vector)
        
        # 정답값
        target_steering_angle_rad = target_rad - heading_rad
        
        # 손실과 보상
        loss = math.sin(target_steering_angle_rad - steering_angle_rad) ** 2
        reward_direction = (1 - loss*2)*2
    
            
    # 보상 합계 reward        
        ########### [reward weight sum] ############ 
        reward = 1*reward_survived + 1*reward_wheels + 1*reward_speed + 1*reward_direction
        
        return float(reward)
    ```
   
<br>
 

## 1. Params

![image](https://user-images.githubusercontent.com/89712324/219348889-b236f971-3feb-4513-ac48-8c1f80199b2e.png)

1. x, y
- x축과 y축에 따른 agent의 중앙 위치
- type: float
- 원점: 왼쪽 하단 모서리
    
    ![image](https://user-images.githubusercontent.com/89712324/219348947-fec2dda0-0307-406a-a68d-5f34a40fe8fc.png)
    
1. speed
    - type: float
    - 범위: 0~4
    - Agent의 속도
2. distance_from_center
    - type: float
    - 범위: 0~(트랙의 너비) / 2
    - Agent의 중앙과 트랙의 중앙 사이의 거리
    - Agent의 한 쪽 바퀴가 트랙의 흰색 경계선을 벗어났을 때 최대
        - (트랙의 너비) / 2
            
            ![image](https://user-images.githubusercontent.com/89712324/219349028-5849dfeb-0119-49fc-ae6d-776698392b92.png)
            
3. track_width
    - type: float
    - 트랙의 너비. 트랙의 흰색 경계선 사이의 거리
4. all_wheels_on_track
    - type: boolean
    - Agent의 트랙 이탈 여부를 확인
        
        ![image](https://user-images.githubusercontent.com/89712324/219349070-d3b008ee-6e60-45dd-8e44-0af2156d75d6.png)
        
5. heading
    - type: float
    - 범위: -180~180
    - 트랙의 x축에 대한 agent의 진행 방향(각도)
        
        ![image](https://user-images.githubusercontent.com/89712324/219349142-19c6a73d-ab9e-4bcf-91b2-df8140839266.png)
        
6. steering_angle
    - type: float
    - 범위: -30~30
    - agent의 진행 방향에 대한 앞바퀴의 각도
    - 앞바퀴가 오른쪽으로 꺾여 있으면 음수, 왼쪽으로 꺾여 있으면 양수
7. steps
    - type: int
    - 완료한 step 개수
        - step: agent가 state 정보를 입력 받고, 현재의 정책에 따라 취할 하나의 행동을 결정하는 일련의 과정 한 번
8. progress
    - type: float
    - 범위: 0~100
    - 트랙을 얼만큼 주행했는지 퍼센트로 반환 (100이면 완주)
9. track_length
    - type: float
    - 트랙의 시작점으로부터 중앙선을 따라 다시 시작점으로 돌아가기까지의 거리
10. is_offtrack
    - type: boolean
    - agent가 종료 시점에 트랙을 벗어났다면 True, 벗어나지 않았다면 False
11. is_left_of_center
    - type: boolearn
    - Agent가 트랙 중앙에서 왼쪽에 있으면 True, 오른쪽에 있으면 False
12. is_reversed
    - type: boolean
    - Agent의 바퀴가 시계 방향으로 주행하면 True, 시계 반대방향으로 주행하면 False
13. waypoints
    - type: [[float, float], [float, float], …]
    - 트랙 중앙을 따라 정해진 waypoint의 위치를 순서대로 나열한 리스트
    - 순환 트랙의 경우 시작과 끝 지점의 waypoint는 같다.
        
        ![image](https://user-images.githubusercontent.com/89712324/219349181-5e7f12db-7c8f-4b03-b369-3b3553ed0bbf.png)
        
14. closest_waypoints
    - type: [int, int]
    - Agent의 현재 위치에서 가장 가깝게 위치한 두 waypoint의 index
    - Agent와의 거리: 유클리드 거리 계산법 이용 (앞바퀴 기준)
    - 첫 번째 값은 agent의 뒤에서 가장 가까운 waypoint, 두 번째 값은 agent의 앞에서 가장 가까운 waypoint
15. closest_objects
    - type: [int, int]
    - Agent의 현재 위치에서 가장 가깝게 위치한 두 object의 index
    - 첫 번째 값은 agent의 뒤에서 가장 가까운 object, 두 번째 값은 agent의 앞에서 가장 가까운 object
    - object가 하나만 있는 경우 두 값은 모두 0
16. is_crashed
    - type: boolean
    - Agent와 다른 object와의 충돌 여부. (충돌 True, 충돌X False)
17. objects_distance
    - type: [float, …]
    - 트랙의 시작점으로부터 object들의 거리
    - i번째 요소는 i번째 object의 시작점으로부터의 거리
18. objects_left_of_center
    - type: [boolean, …]
    - object들이 중앙선을 기준으로 왼쪽에 있는지 오른쪽에 있는지 (왼쪽 True, 오른쪽 False)
19. objects_heading
    - type: [float, …]
    - object들의 x축에 대한 object의 진행 방향(각도) (움직이지 않는 object는 0)
20. objects_location
    - type: [[float, float], [float, float], …]
    - object들의 위치값(x, y)
21. objects_speed
    - type: [float, …]
    - object들의 speed (움직이지 않는 object는 0)

<br>

## 2. 기본 보상함수 예제

### 중앙선 따라가기

![image](https://user-images.githubusercontent.com/89712324/219349221-cbd85d91-ed14-4998-b56b-7a9c6ad2a267.png)
<br>


### 두 경계선 안에서 움직이기

![image](https://user-images.githubusercontent.com/89712324/219349264-4e484f41-5deb-4270-8f68-7c76a3ea141c.png)
<br>


### 지그재그로 움직이는 것 방지하기

![image](https://user-images.githubusercontent.com/89712324/219349307-370ce26b-d980-465e-9005-e67deee814d3.png)
<br>


### 장애물 피하기 & 부딪히지 않고 한 줄로 가기

![image](https://user-images.githubusercontent.com/89712324/219349360-11a4a0f8-b71b-480f-ae36-d5794a74848e.png)

![image](https://user-images.githubusercontent.com/89712324/219349416-28ea3682-94b6-4e61-937a-0bfa1b8dc871.png)
<br>
<br>



## 3. 보상함수 실험

- 보상을 항상 1로 준다 Discount factor 0.999⇒ 완주율 100%
    - 생존하면 1이고 미래 보상 가치도 크기 때문에 오래 살아남을 수 있어 완주률이 높다.
- 보상을 항상 1로 준다. Discount factor 0으로 준다. ⇒ 전혀 학습 안된다.
    - 미래 보상 가치가 없기 때문에 당장 현재 생존만 하면 되므로 완주율이 매우 낮다.
- 보상을 항상 -1로만 준다. Discount factor 0.999 ⇒ 완주율이 줄어든다.
    - 생존할 가치가 없기 때문에 완주율이 줄어든다.

<br>

## 4. Track 시각화

[https://drive.google.com/file/d/1tR6NCXrD56Hc5IvgtlMl8C5J6HsqHGVT/view?usp=sharing](https://drive.google.com/file/d/1tR6NCXrD56Hc5IvgtlMl8C5J6HsqHGVT/view?usp=sharing)

<br>

<br>

# Log Analysis

## 1. Reward graph

- 모델 성능을 개선하기 위해 로그 기록을 분석해보자
- 훈련에 대한 결과를 확인할 수 있다.
    
    ![image](https://user-images.githubusercontent.com/89712324/219349886-be6e27ad-4ddd-4865-9f65-0e13e22fc585.png)
    
    - 모두 우상향을 그려야 정상적인 그래프이다.
    - 보상 축 스케일은 계속 변하며 완주율 축 스케일은 고정이다.
    - 그래프
        - 초록색 - 한번 iteration 시에 얻을 수 있는 평균 보상
        - 파란색 - 훈련 시 평균 완주율 : 트랙 전반적인 부분에서의 완주율을 알 수 있다.
        - 빨간색 - 평가 시 평균 완주율 : 트랙 시작점에서 출발했을 때의 완주율을 알 수 있다.
    - 초록색은 상승 & 파란색은 하강
        - 안정성을 버리고 보상을 추구한다는 의미 ⇒ 보상 체계 잘못됨, 보상 함수에서 안정성에 더 높은 보상을 줄 필요가 있다.
    - 빨간색과 파란색은 함께 상승하며, 빨간색이 파란색 보다 더 위쪽에 있는 것이 일반적이다.
        - Training은 트랙 여러군데서 출발점을 가지지만, Evaluation은 트랙 시작점에서만 출발한다.
    - 점 하나 - 업데이트 한번
        - 점 하나는 에피소드가 20개씩 담겨있는 하나의 iteration이다. (이탈하면 에피소드 끝)
        - iteration 5에서의 보상은 에피소드 20개의 평균(에피소드의 보상 합 / 에피소드 개수 나누기)인 120 이다.
    

<br>

## 2. download logs

1. 시뮬레이션을 통해
2. 그래프를 통해
3. 모델의 로그를 통해 분석할 수 있다.
    - [Download logs] 클릭

        ![image](https://user-images.githubusercontent.com/89712324/219349515-8dd6c128-c6d0-414d-a882-c726d5de4eb0.png)

<br>

### logs/…/~~-sagemaker.log

- 훈련 중 에러 발생 시, 이 파일의 하단의 에러 메시지를 확인하여 해결하기

### logs/…/~~-robomaker.log

- 보상함수의 print 내용은 이 곳에 출력된다.

### metrics/…~~.json

- Metrics에 대한 로그 기록들이 담겨있다.
    
    ![image](https://user-images.githubusercontent.com/89712324/219349578-0a2133d0-3cbd-4c6e-be2a-59b671a55766.png)
    

### sim-trace/…/n-iteration.csv

- 각각의 iteration마다 시뮬레이션 상태 값에 대한 기록들이 담겨있는 csv 파일
- 데이터가 밀려있는 경우가 있으므로 주의하기!

<br>


## 3. iteration csv 파일 다루기

[https://drive.google.com/file/d/1tRCSMSFR9_Y5SCerCFGuP-yl0XSOHInR/view?usp=sharing](https://drive.google.com/file/d/1tRCSMSFR9_Y5SCerCFGuP-yl0XSOHInR/view?usp=sharing)  
<br>


## 4. 모든 params 출력하기

[https://drive.google.com/file/d/1tevvBHmGi3XjdnC8PDv_I6muHCDu45Yt/view?usp=sharing](https://drive.google.com/file/d/1tevvBHmGi3XjdnC8PDv_I6muHCDu45Yt/view?usp=sharing)