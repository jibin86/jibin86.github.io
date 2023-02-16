---
title:  "[데이터 전처리] End-to-End Data Mining"
categories: [Data Engineering, Data handling]
tags: [전처리, Normalization,Standardization, encoding, PCA, LDA]
---

전 포스팅에서 데이터 전처리하는 법을 복습하였다. 사이킷런의 와인 데이터를 가지고 end-to-end 실습해보자

## 1. Load Data

![image](https://user-images.githubusercontent.com/89712324/216984531-c81f72a6-2f55-4601-8c49-407edb60b9d5.png)


## 2. Sanity check

![image](https://user-images.githubusercontent.com/89712324/216984574-5262a95f-0067-4251-9d4b-ac5f5721cc0d.png)

## 3. Feature selection

- 상관계수를 이용하여 feature filtering을 해주었다.
    
    ![image](https://user-images.githubusercontent.com/89712324/216984641-45e92765-c1dc-4992-8f44-5c8f7aebd581.png)
    
    - 상관계수가 0.6보다 큰 특성만 사용한다.
        
        ![image](https://user-images.githubusercontent.com/89712324/216984686-1afb1c5d-feb3-449b-87f9-2a0d8e7ad18d.png)
        
- plot을 그려 데이터를 잘 설명할 수 있는 특성을 선택해보자
    
    ![image](https://user-images.githubusercontent.com/89712324/216984818-b418e964-0eba-428b-bd72-b4e9c0296240.png)
    
- Transforming data
    - 모든 특성을 이용해서 PCA와 LDA를 비교해보자
    
    ![image](https://user-images.githubusercontent.com/89712324/216985049-c0f97b6f-0a4a-4e87-b1ab-bf318757ecb9.png)
    
    ![image](https://user-images.githubusercontent.com/89712324/216985158-92b784e4-a025-4979-aca9-3c3423262ffa.png)
    
    - 실루엣 스코어로  PCA와 LDA의 성능을 비교할 수 있다.
        
        ![image](https://user-images.githubusercontent.com/89712324/216985201-e871baba-5b4d-48eb-af1a-2a19199cfcdf.png)
        
        - LDA의 클래스가 데이터를 더 잘 분류할 수 있었다. 사실 LDA가 라벨이라는 데이터를 가지고 있었기에 당연한 결과이다.