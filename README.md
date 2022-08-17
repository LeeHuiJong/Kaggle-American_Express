# Kaggle-American Express - Default Prediction

<p align="left">
    <img src="images/amex pic.png">
</p>

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
                 
## 2. EDA
- 본 경진 대회의 '목적'은 American Express 사의 카드를 사용하는 고객들이 상환 날짜가 연체된 비용을 지불 가능한지 예상하는 것이다. 먼저 train 셋에 있는 target columns을 이용하여
Default(연체)와 Paid(지불)의 비율을 알아 보았다.
<p align="left">
    <img src="images/target_distribution.png">
</p>

- 먼저 인지하고 가야할 사항은 test 셋의 샘플수와 submission 셋의 샘플수가 일치하지 않는다는 점이다. submission 셋에는 <br>
  중복되는 고객ID가 없고 test셋에는 존재한다.

- 원 그래프는 'customer-Id' 별 데이터의 갯수이다. 13개의 데이터가 가장 많았다. 이는 실제 1사람의 기록이 13명의 기록처럼 가공이 될 우려가 있다. 이를 방지하기 위해(모든 customer들의 연체금 지불 여부를 알기위해) 각 customer-Id 별 가장 마지막 거래만 뽑아서 데이터를 가공하였다.

<p align="left">
    <img src="images/train_test statements per customer.png">
</p>

- 190개의 columns을 Deliquency, Spend, Payment, Balance, Risk 등 5가지로 나눌 수 있었다. 데이터를 처리할 때 많은 데이터를 처리하는 것 뿐만 아니라 데이터의 결측치를 어떻게 해결해 주어야 할지도 프로젝트를 진행하는 데에 힘이 들었던 부분이다.

<p align="left">
    <img src="images/deliquency_1.png">
</p>
<p align="left">
    <img src="images/deliquency_2.png">
</p>
<p align="left">
    <img src="images/spend_1.png">
</p>
<p align="left">
    <img src="images/payment_1.png">
</p>
<p align="left">
    <img src="images/balance_1.png">
</p>
<p align="left">
    <img src="images/risk_1.png">
</p>

- 위는 각 columns 당 데이터 결측치의 정도를 알 수 있다. 막대 그래프의 세로 크기는 결측치가 아닌 정상 데이터의 양을 의미하고, 절반 이상의 칼럼에 결측치가 발생함을 알 수 있다.

### 2.1. 연속형 데이터
- 우선적으로 확인할 수 있었던 점은 고객별로 가지고 있는 내역의 수가 1~13개 였다는 점이다.
- 위 결과를 참고해 각 고객의 내역들을 취합해 평균, 표준편차, 최소, 최대값 등의 통계수치를 파생변수로 추가.
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

### 2.4. EDA 결론 & 전처리 
- 결측치를 처리하는 방향은 범주형(categorical),이진분류(binary) 데이터는 새로운 unique 값을 생성하기로 결정(결측치가 상당한 부분을 차지하기 때문에)
- 연속형(numeric) 데이터는 결측치를 0으로 대체.(비식별 데이터에서 연속형 결측치는 대부분 0을 나타내는 경우가 많음)
- train과 test의 customer id 가 중복되는 수가 존재하는 것을 확인했고 unique 값으로 확인했을때 label의 샘플수와 일치하는 것을 확인.
- 위 사실을 토대로 같은 id의 값들을 groupby로 묶어 평균을 내는 전처리를 선택
- 평균 하나로는 피처의 특성을 나타내기 힘들것 같아 min, max, std 등의 통계값을 파생변수로 생성
- 범주형 데이터는 평균을 낼 수 없기 때문에 동일 범주(customer id) 개수, 마지막 값, 고유값의 개수(nunique)들로 파생변수를 생성 
<br>

## 3. 모델 선정
- 우선 세가지 머신러닝 XGBoost, Catboost, LGBM 모델이 가장 많이 쓰이고 있었기 때문에 사용.
### 3.1 XGBoost
- 가장 보편적인 모델, 많은 사람들이 XGB모델을 사용해서 준수한 예측 결과를 도출해냄(4500팀중 1500등 정도의 성능을 지님).
- 준수한 성능을 보여주었고 약간의 결측치 처리에 변화를 주어도 성능이 어느정도 상승함<br>->[결측치 변화 후 시행 결과](https://github.com/LeeHuiJong/Kaggle-American_Express/blob/main/%EB%AA%A8%EB%8D%B8%EC%8B%9C%ED%96%89/amex-fold-10-nan-0.ipynb)
### 3.2 Catboost
- XGB와 마찬가지로 boost 모델의 종류중 한가지. 파라미터가 xgb보다 많기 때문에 훈련 과정에 시간이 많이 걸릴것으로 예상
- 전처리에 따라서 어느정도 성능의 변화를 보였고
### 3.3 LGBM
- 대용량 데이터를 사용하기에 적합 10000개 이하의 데이터 사용시 과적합이 일어나기 때문에 소규모 데이터 셋에는 적절하지 않음
- boosting 파라미터를 dart 로 설정해주는 LGBM dart 모델이 가장 많이 쓰이면서 좋은 결과를 보여줌 (0.797)
### 3.4 AutoML
- AutoML에는 Catboost, lightgbm(LGBM), XGBoost 뿐 만 아니라 gbr, rf, et, ridge 등 20개의 모델을 사용.
- 한 번 시행시 데이터를 모든 모델에 넣어보기 때문에 경제성이 매우 떨어지는 단점.
- train(약 460,000개)의 크기를 2,000 / 10,000 / 12,000 / 15,000 / 30,000 개로 증가시켜 시행 해 본 결과 0.725/ 0.756/ 0.757/ 0.764/ 0.765로 train 데이터가 커질수록 좋은 결과가 나오는 것을 확인.

<p align="center">
    <img src="images/AutoML_Model_evaluation.PNG">
</p>

### 3.5 앙상블
- 결과가 좋았던 모델들 중 4~5개를 모아서 각 모델별로 가중치를 달리해 예측값을 취합.
- XGBoost, AutoML(30,000), LGBM, Overfitting 과 같은 모델들을 사용해 앙상블. 특히 대규모 데이터 셋에 적합한 LGBM은 각기 다른 성능의 LGBM 모델들을 다수 사용.
- 결과는 다음과 같다.

<p align="center">
    <img src="images/Ensemble_submit_pred.png">
</p>

<p align="center">
    <img src="images/submit.jpg">
</p>


## 4. 모델 시행 결과
- 사용 변수 결측치 수정 및 다양한 모델 사용하여 예측 모델을 구현

## 5. 추론 및 검증 결과 

## 6. 프로젝트 결과
