# Kaggle-American_Express

## 0. 대회 정보
- 대회 목표: American Express 카드사의 고객들이 향후 신용카드 잔액을 납부하지 않을 확률을 예측.
            120일 내에 납부하지 않을 경우 기본 이벤트로 간주함.(납부함 = 1, 납부안함 = 0)
- 주최기관: American Express
- 대회링크 (https://www.kaggle.com/competitions/amex-default-prediction/overview)

## 1. 데이터
- train data : 총 5531451 samples, 190 columns
- test data : 총 924621 samples, 190 columns
- 데이터 컬럼 설명 :  
                     D_* = Delinquency: 과실, 태만, 체납 -> 연체 변수  
                     S_* = Spend. 소비 변수  
                     P_* = Payment. 지급 변수  
                     B_* = Balance. 균형 변수  
                     R_* = Risk. 위험 변수
- [데이터 원본](https://www.kaggle.com/competitions/amex-default-prediction/data)
- 외부 데이터 사용여부 : 카드사 자체에서 제공한 데이터이기 때문에 연관성 있는 외부데이터 사용불가.
                 
## 2. EDA를 기반으로 한 전처리 방향

<p align="left">
    <img src="images/statement_per_customer.png">
</p>

### 2.1. 연속형 데이터
- 우선적으로 확인할 수 있었던 점은 고객별로 가지고 있는 내역의 수가 1~13개 였다는 점이다.
- 위 결과를 참고해 각 고객의 내역들을 취합해 평균, 표준편차, 최소, 최대값 등의 통계수치를 파생변수로<br>
  추가.
<br>

### 2.2. 범주형 데이터
<p align="left">
    <img src="images/categorical_features.png">
</p>
- 범주형 데이터는 B_30, B_38, D_114, D_116, D_117, D_120, D_126, D_63, D_64, D_66, D_68 총 11개다.</br>
- 연속형 데이터와는 다르게 범주의개수, 마지막값, 고유값개수 들을 파생변수로 설정
- 문자형 데이터들은 모두 라벨인코더를 사용해 정수로 변경
<br>

### 2.3. 이진분류 데이터

<p align="left">
    <img src="images/binary_features.png">
</p>
- 범주형 데이터와 비슷하지만 1,0 또는 1,nan 으로 이루어진 컬럼이 있었다.
- 범주형과 마찬가지로 결측치가 상당한 부분을 차지하기에 새로운 unique값으로 할당
<br>

## 3. 모델 선정

- 우선 세가지 머신러닝 XGB, Catboost, LGBM 모델이 가장 많이 쓰이고 있었기 때문에 사용.
### 3.1 XGBoost
- 가장 보편적인 모델, 코드를 공유해주는 많은 사람들이 XGB모델을 사용함.
- 준수한 성능을 보여주었고 약간의 결측치 처리에 변화를 주어도 성능이 어느정도 상승함<br>->[결측치 변화 후 시행 결과](https://github.com/LeeHuiJong/Kaggle-American_Express/blob/main/%EB%AA%A8%EB%8D%B8%EC%8B%9C%ED%96%89/amex-fold-10-nan-0.ipynb)
- 파라미터 변경에 가장 적게 영향을 받는 모델
### 3.2 Catboost

### 3.3 LGBM
- 가장 좋은 단일 모델 성능을 지니고 있었고 
