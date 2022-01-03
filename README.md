![image](https://user-images.githubusercontent.com/38115693/147531950-53db2283-13f0-4704-b97c-0daf513bf8f7.png)

---
## Introduction
- 실제 진료 데이터를 기반으로 당뇨병 예측 서비스 개발

---

## Contents
- [1. 프로젝트 소개](#1-프로젝트-소개)
  * [배경](#배경)
  * [프로젝트 개요](#프로젝트-개요)
- [2. EDA 및 데이터 전처리](#2-데이터-전처리)
  * [결측치 처리](#결측치-처리)
  * [데이터 범주화](#데이터-범주화)
  * [파생변수 생성](#파생변수-생성)
  * [EDA](#EDA)
- [3. 모델링](#3-모델링)
  * [모델링 과정](#모델링-과정)
  * [모델 성능 평가](#모델-성능-평가)
  * [최종 모델](#최종-모델-final-model)
- [4. 프로젝트 한계 및 과제](#4-프로젝트-한계-및-과제)
  * [한계점](#한계점)
  * [향후 과제](#향후-과제)
- [5. 참고 문헌](#5-참고-문헌)

---

## 1. 프로젝트 소개
### 배경
- 세계보건 기구에서 세계 당뇨의 날을 지정 할 만큼 당뇨의 유병률과 심각도가 증가하고 있습니다.
- 코로나19 이후 실내 활동 증가로 인한 일상 생활 활동량 감소와 운동 빈도가 줄고 운동량의 감소로 체중이 증가하여 당뇨에 대한 위험성이 증가하고 있습니다. 
- 당뇨는 그 자체보다 심근경색, 뇌졸증, 망막질환, 신장질환 등과 같은 합병증을 일으킬 수 있는 질환입니다.
- 사전에 당뇨병을 예측해 자가관리 및 질병 치료에 도움을 주고자 프로젝트 시행 하게 되었습니다.

### 프로젝트 개요
1. 프로젝트명: **머신러닝 기반 제2형 당뇨병 진단 사전 예측 모형**
2. 수행자: 김대영, 박성호
3. 수행기간: 2주 (2021.12.07 \~ 2021.12.21)
4. 목표: 데이터 수치들을 바탕으로 당뇨병 예측
5. 데이터셋: 당뇨병 데이터
    - 총 2438개의 행과 26개의 컬럼으로 구성 돼 있습니다.
        - 환자에 대한 기본 정보(나이, 성별, 키, 몸무게, 체질량지수)
        - 혈압 정보
        - 콜레스테롤 수치
        - 간 수치
        - 신장 수치
    - 총 2438개의 행 중 당뇨병을 진단받은 데이터는 206개로 구성 돼 있습니다.
        - **불균형 데이터**

---
## 2. 탐색적 데이터 분석

### 결측치 처리
<p align='center'><img src="https://user-images.githubusercontent.com/38115693/147493976-370db9bb-482e-4367-a416-b4b531f6383e.png" width="60%"></p>

- `PR`, `TG`, `LDL`, `HDL`, `Alb`, `ALP` 피처에서 결측치가 존재 했습니다.
    - `PR`의 경우 나이대 별 중위값으로 값을 대체 하였습니다.
    - `TG`의 경우 `TC = LDL + HDL + TG/5`의 산식을 이용해 대체 했습니다.
    - 254번 인덱스의 경우 `TG`, `LDL`, `HDL`, `Alb`, `AlP` 모두 존재하지 않아 데이터를 삭제 하였습니다.

### 데이터 범주화
- [컬럼 정의 및 분류 기준](https://docs.google.com/document/d/1r3cG6r4KdIx6VLw8R8tiUZr7N-1v2GtEt5wEvDYpa18/edit#heading=h.z2foisebalcq)을 바탕으로 정상 또는 판단 기준 범위에 따라 나눴습니다.

### 파생변수 생성
- 당뇨병 유관 질병
    - `DLP(이상지질혈증)`, `MS(대사증후군)`
- 당뇨병 진단과 관련 된 검사
    - `맥압`
    - `BUN/Cr` 비율
    - `AST/ALT` 비율

### EDA
:bar_chart: **Feature별 분포도**
![output](https://user-images.githubusercontent.com/91659448/147900436-60db796c-4892-443a-aa2e-cc7ff305b0d3.png)
)
- 각 Features들의 이상치가 존재하나 의학적으로 의미가 있는 수치들이였습니다.

:bar_chart: **당뇨병 유무별 피쳐 분포도**
![output](https://user-images.githubusercontent.com/91659448/147629446-e11dcee0-e6b7-4bb5-b871-a6c02e536061.png)

- 혈당과 관련 된 피쳐들(`HbA1c`, `FBG`)은 당뇨병 진단을 받은 집단이 좀 더 높은 값에 분포하고 있습니다.
- 고밀도 콜레스테롤(`HDL`)은 당뇨병 진단을 받은 집단이 좀 더 낮은 값에 분포하고 있습니다.

![image](https://user-images.githubusercontent.com/91659448/147900305-a7b6a88a-2631-4aa4-963a-5bc4e59cfac9.png)

- 당뇨 위험도 측정표에 따르면 `나이`, `혈압`, 허리둘레와 관련있는 `BMI`가 위험의 요인으로 꼽고있습니다.
- 이를 확인하기 위해 상관계수를 살펴보았습니다.

:bar_chart: **Feature별 상관계수 히트맵**

![image](https://user-images.githubusercontent.com/91659448/147900114-fc0470dd-115e-480b-a9b4-1e200f554335.png)

- `나이`와의 상관은 `-0.06`으로 상관 관계가 거의 없어 보입니다.
- `혈압`과 `BMI`도 크게 상관이 없어 보입니다.
- 하지만, `혈당`과 관련 된 수치는 직접적인 당뇨 판단 요인인 만큼 약한 상관관계를 보입니다.
- 해당 변수들에 대한 분포도를 확인해보고 어떤 경향성이 있는지를 파악해 보겠습니다.

:bar_chart: **BMI별 당뇨 분포도**

![image](https://user-images.githubusercontent.com/91659448/147900267-e2fa0f2f-1dd7-429a-8b78-6094f16fff44.png)

- `BMI` 지수가 증가할수록 당뇨 환자의 비율이 높은 편입니다.

:bar_chart:  **혈압별 당뇨 분포도**

<img src='https://user-images.githubusercontent.com/91659448/147900697-3cab4cda-aaba-4d00-b541-13023846b341.png' width=800>

- 당뇨 그룹의 집단이 혈압이 높은 환자들의 비중이 더 많았습니다.

:bar_chart: **혈당별 당뇨 분포도**

![image](https://user-images.githubusercontent.com/91659448/147900633-82370d40-1aa3-4063-99bd-cbf396d1e2f0.png)

- 혈당이 높은 그룹이 낮은 그룹보다 당뇨의 비율이 높은 편입니다.

---
## 3. 모델링<a class="anchor" id = "c4"> </a>

### 모델링 과정

![image](https://user-images.githubusercontent.com/38115693/147495960-f9e2e82c-6d3d-4d84-a875-f63e3d33732a.png)

모델링은 크게 세 가지 과정을 거친다.
1. 데이터 전처리
2. Imbalanced 데이터 처리를 위한 오버샘플링
3. 파라미터 튜닝

### 사용한 데이터 처리 기법 및 학습/예측 모델

![image](https://user-images.githubusercontent.com/38115693/147496168-a3211c99-e055-4805-bbf3-1771d3072fcf.png)

1. 범주형 데이터를 사용한 모델링시 인코딩은 **Label Encoding**과 **One-Hot Encoding**을 각각 사용하였고, 연속형 데이터만으로 **Standard Scaling**을 따로 적용하여서도 모델링해 보았다.
2. Label 2437개 중 0: 2231개 / 1: 206개의 불균형 데이터이기 때문에, 불균형 데이터 처리를 위해 리샘플링을 하였는데, 데이터 양이 많지 않아 오버샘플링을 하기로 결정하였다.
3. DecisionTree, KNN, LogisticRegression, RandomForest, XGBoost, SVM 분류기 등 9개의 모델을 사용해서 학습과 모델링을 진행하였다.

### 세부 모델링 과정

#### 1. 데이터 전처리

![image](https://user-images.githubusercontent.com/38115693/147497046-b62286eb-5ead-44c8-9515-808e3f3152ab.png)

모델링은 3가지로 나눠서 접근해 시도하였다.

우선, 기존의 각 검사수치 변수(연속형)들을 그룹핑하여 범주화하였고, 해당 범주형 변수들로만 학습/예측 모델링을 시도했다. 범주형 데이터 처리를 위해 인코딩을 하였는데, 아래와 같은 이유로 Label Encoding과 One-Hot Encoding을 각각 진행하게 되었다:
1. 데이터를 그룹화 하여 나눠 놨지만, 레이블 인코딩을 했을 때 각각의 수치 값이, 예를 들어, 정상 미만(0), 정상(1), 정상 이상(2) 등과 같은 순서가 있는 데이터처럼 되었기에, 레이블 인코딩를 사용해도 각 수치 값의 의미가 보존되지 않을까 생각하여 레이블 인코딩도 진행하였다.
2. 선형 계열 모델(로직스틱회귀, SVM, 신경망 등)의 경우 숫자의 차이가 모델에 영향을 미치기 때문에, 레이블 인코딩된 결과에 이에 대한 영향이 있을 수도 있어 원핫인코딩 또한 별도로 적용하여 진행하였다.

마지막으로, 당뇨병에 대한 조사를 하면서, 각 검사 수치에 대한 판단 기준이 자료마다, 병원마다 달랐고, 따라서 조사한 것과 자료들을 기반으로 판단하여 범주화한 것이 예측 성능에 영향을 줄 수도 있을 것 같아 기존의 연속형 변수들만을 사용하여 Standard Scaling 적용 후 모델링하는 것 또한 시도하였다.

그리고 성능 테스트 과정에서 의미가 있을 것으로 판단되는 경우 범주화된 데이터셋에 일부 스케일링된 변수를 추가하거나, 반대로 스케일링된 데이터셋에 범주형 변수를 합쳐서도 시도하였으며, 불필요하다고 여겨지거나 상관관계가 높은 변수들을 빼보기도 하였다.

#### 2. 오버샘플링

![image](https://user-images.githubusercontent.com/38115693/147497557-b2dab919-761d-4e4f-bd84-620556ee9de4.png)

오버샘플링은 5가지 기법을 사용하였으며, 적용 대상 데이터셋의 특성(범주형, 연속형, 범주형+연속형)에 맞춰 적절한 기법을 사용했다.

1. `Random Oversampling(ROS)`: 기존에 존재하는 소수의 클래스(Minority)를 복제하여 비율을 맞춰주는 기법다. 숫자가 늘어나기 때문에 더 많은 가중치를 받게 되는 원리이다.
2. `SMOTE`: 임의의 소수 클래스 데이터로부터 인근 소수 클래스(minor class) 사이에 새로운 데이터를 생성하는 기법이다. 임의의 소수 클래스에 해당하는 관측치 X를잡고, 그 X로부터 가장 가까운 K개의 이웃을 찾아 그 사이에 임의의 새로운 X를 생성하는 데이터로부터 인근 소수 클래스 사이에 새로운 데이터를 생성한다. 
3. `ADASYN`: Borderline-SMOTE에서 조금 더 변주를 준 알고리즘으로, 인접한 다수 클래스의 비율에 따라 가중치를 줘 SMOTE를 적용시키는 방식이다.
4. `SMOTE-NC`: SMOTE가 수치형/연속형 데이터로만 이루어진 데이터에 적합한 반면, SMOTE-NC는 범주형과 수치형이 믹스된 데이터에 적용할 수 있는 기법이다.
5. `SMOTE-N`: SMOTE-N은 범주형으로만 이루어진 데이터에 적합한 SMOTE 기법이다.

#### 3. 하이퍼파라미터 튜닝

![image](https://user-images.githubusercontent.com/38115693/147499588-35f33d6b-5fb9-4902-93bf-a9c3bdb873e1.png)

각 전처리 및 오버샘플링 과정별로 하이퍼파라미터 튜닝을 진행하였는데, DecisionTree와 KNN을 베이스 모델로 시작하여, 다른 여러 모델들을 시도해 보면서 성능을 비교하며 파라미터 옵션을 추가하거나 값을 변경해 갔다.

암 진단과 같이 당뇨병 진단도 실제 양성을 양성으로 예측하는 것이 중요하다고 판단하여, **accuracy(정확도)와 함께 recall(재현율) 예측률을 높이는 것에 중점**을 두어 파라미터 튜닝을 진행하였다.

![image](https://user-images.githubusercontent.com/38115693/147500056-cbd7a551-021a-4775-8f66-d14874de4910.png)

이후, 각 과정별 성능이 좋은 모델을 선별하여 교차검증과 GridSearchCV를 사용하여 최적의 파라미터를 찾아보았다. 성능을 테스트해 가며 파라미터 옵션이나 값을 추가하거나 변경해 갔다. 

하지만 기대했던 좋은 결과를 얻지 못 하여, 주요 모델에 대해 GridSearchCV에 구체적인 평가(evaluatoin) 메트릭스를 단일 지정하여서 시도해 보고, 다중으로 지정하여서도 파라미터 튜닝을 시도해 보았다.

![image](https://user-images.githubusercontent.com/38115693/147500254-bc1d381d-6bd5-4cb6-865d-cc6e9479a1ff.png)

그럼에도 기대했던 좋은 결과를 얻지는 못하였다. Accuracy에 대해선 높은 결과를 얻을 수 있었지만, accuracy와 recall 모두 좋은 성능을 보이는 모델을 찾기는 쉽지 않았다. 직접 튜닝을 했을 때의 결과가 더 이상적이었기 때문에, 결국 직접 튜닝해 가며 여러 시나리오별로 학습과 테스트를 진행하였다.

그리고 여러 evaluation metrics로 평가해 mean값을 정리해 주고자 cross_val_score 모듈을 사용하지 않고, Stratified K-Fold와 반복문을 사용해 index를 반환받아 교차 학습 및 평가를 진행하였으며, 각 모델별로 평가 지표(accuracy, precision, recall, f-1, roc-auc)의 평균 값으로 모델 성능을 평가하였다. 이와 같이 진행하며, accuracy와 recall에 대한 성능을 높여나갔다.

![image](https://user-images.githubusercontent.com/38115693/147502491-7d5359ca-5468-4df2-b4df-99d05f03380a.png)


---
## 모델링 결과<a class="anchor" id = "c5"> </a>

### 각 수치를 구간별로 나눠 범주화 한 데이터 기준 모델링

우선 각 수치별로 기준 범위에 따라 구간으로 나누고 범주화 하여 생성한 데이터를 사용하였다.
- (1) One-Hot Encoding을 적용하고,
- (2) 별도로 범주화하지 않은 연속형으로 남아있는 Wt, Ht 피처들을 포함하여 그리고 포함하지 않고 Standard Scaling을 적용하여 합쳤다.
- (3) 이후, 오버샘플링으로 범주형+연속형 데이터에 적합한 SMOTE-NC와 RandomOversampling를 적용하였다.

테스트 결과 중, ROS을 적용했을 때의 모델의 예측 성능이 가장 좋았으며, (2) 연속형인 Wt, Ht를 제외하고, 범주화 한 피처들만을 사용했을 때가 결과적으로 더 나은, 아래와 같은 예측 성능을 가져왔다.

<p align="center"><img src="https://user-images.githubusercontent.com/38115693/147502365-93d6dfdc-13af-4d72-bcc2-f4812c19e3ea.png" width="40%" height=""></p>

One-Hot Encoding과 ROS를 적용한 테스트 결과는 평균적으로 이와 같은 예측 성능을 얻었다.
- Random Forest: accuracy 82%, recall 75%
- Logistic Regression: accuracy 79%, recall 74%
- SVM: accuracy 77%, recall 74%

### 기존 연속형 데이터 기준 모델링

기존 연속형 데이터 기준으로 Standard Scaling을 적용하여 모델링을 하였다.

- (1) 우선 gender, age 피처에 대해 One-Hot Encoding을 적용하고,
- (2) 'Data Leakage'를 피하기 위해 train-test split을 먼저 한 후, 기존 연속형 변수들에 대해 Standard Scaling을 적용하였다.
- (3) 마지막으로, oversampling(SMOTENC, RandomOversampling) 기법을 적용하고 모델 학습과 테스트를 진행한 결과는 아래와 같다.

<p align="center"><img src="https://user-images.githubusercontent.com/38115693/147503805-fc4534c4-4bc3-454a-b1fb-79cc92202e5b.png" width="75%" height=""></p>

SMOTE-NC를 적용했을 때에, 평균적으로 이와 같은 예측 성능을 얻었다.
- Logistic Regression: accuracy 83%, recall 79%
- SVM: accuracy 81%, recall 79%

Random Oversampling을 적용했을 때엔, 평균적으로 이와 같은 예측 성능을 얻었다.
- Logistic Regression: accuracy 83%, recall 80%
- SVM: accuracy 82%, recall 81%

Standard Scaling을 적용한 두 모델 모두 성능은 비슷하게 나타났으며, 두 결과 모두 구간으로 나누어 범주화한 데이터셋을 사용하여 모델링한 결과보다 성능이 더 좋게 나타났다.

**상관관계가 높은 변수 처리 후 재시도**

이어서 상관관계가 높은 변수를 처리한 후 다시 동일하게 학습과 테스트를 진행하였다.
- Wt(몸무게), Ht(키)간의 그리고 BMI(체질량지수)와 Ht간의 양적 상관관계가 높고, BMI에 이미 Wt, Ht가 사용되어 계산된 것이기 때문에 Wt, Ht 피처는 제외하였다.
- DBP(이완기혈압), SBP(수축기혈압)도 높은 양의 상관관계를 보이나, DBP와 SBP의 차이('맥압') 또한 의미가 있을 수 있다 생각되어 제외하지 않기로 하였다.
- TC(총 콜레스테롤)를 계산하자면 HDL+LDL+(TG/5)이며, LDL이 TC 계산에 포함되고 양적 상관관계 또한 높아 학습시 해당 변수를 제외할까 고민했지만, 이 계산식은 어디까지나 간이 계산법이며, 검사를 한 것이 보다 정확한 수치라고 한다 (콜레스테롤의 경우 오차범위가 큰 편이다). 따라서, TC, HDL(고밀도콜레스테롤), LDL(저밀도콜레스테롤), TG(중성지방) 각각의 수치가 가지는 의미나 영향이 있을 수 있다 생각되어, 제외하기 않고 사용하였다.
- ALT(알라닌아미노전이효소), AST(아스파르테이트아미노전달효소) 두 피처도 높은 양의 상관관계를 보이는데, AST/ALT 비율이 간 기능의 데미지를 파악하는 지표로도 사용되기에 제외하지 않고 사용하였다.
- CrCl(크레아티닌청소율)과 Cr(크레아티닌)은 비교적 강한 음의 상관관계를 보인다. Cr은 낮을수록, CrCl은 높을수록 좋은 것인데, Cr이 증가하면 CrCl은 감소한다. Cr과 CrCL 수치엔 연령, 체중, 성별 등의 변수도 영향을 주기에 두 피처 모두 그대로 포함하여 진행하였다.

<p align="center"><img src="https://user-images.githubusercontent.com/38115693/147504188-68227819-e075-4056-989c-174eb6976814.png" width="75%" height=""></p>

SMOTE-NC를 적용했을 때에, 평균적으로 이와 같은 예측 성능을 얻었다.
- Logistic Regression: accuracy 84%, recall 77%
- SVM: accuracy 82%, recall 77%

Random Oversampling을 적용했을 때엔, 평균적으로 이와 같은 예측 성능을 얻었다.
- Logistic Regression: accuracy 83%, recall 80%
- SVM: accuracy 82%, recall 82%

Wt, Ht 피처들을 제외한 두 모델도 구간으로 나누어 범주화한 데이터셋을 사용하여 모델링한 결과보다는 예측 성능이 더 좋은 것으로 보여진다. Wt, Ht를 포함하여 모델링 했을 때의 결과와 비교하면, accuracy 결과는 비슷하지만, recall은 Wt, Ht 피처들을 포함했을 때의 결과가 더 좋기 때문에, Wt, Ht 피처들을 포함하는 경우 더 좋은 모델로 판단된다.

다음으로, 기존 연속형 피처들을 Standard Scaling을 적용하는 것에 age 피처를 포함하여 진행하였다. 
- (1) gender 피처에 대해서만 One-Hot Encoding을 적용하고,
- (2) 이번엔 age 피처를 Standard Scaling 과정에 포함하여 다른 연속형 변수들과 함께 스케일링하여 진행하였다.
- (3) 그리고 앞서 설명한 과정과 동일한 과정을 거쳐 학습과 테스트를 진행하였으며, Wt, Ht 피처들을 포함하였을 때의 결과가 아래와 같다.

<p align="center"><img src="https://user-images.githubusercontent.com/38115693/147504845-ca463740-bcfd-4eb4-9f09-9d6a08092a8b.png" width="75%" height=""></p>

SMOTE-NC를 적용했을 때에, 평균적으로 이와 같은 예측 성능을 얻었다.
- Logistic Regression: accuracy 85%, recall 82%
- SVM: accuracy 84%, recall 82%

Random Oversampling을 적용했을 때엔, 평균적으로 이와 같은 예측 성능을 얻었다.
- Logistic Regression: accuracy 84%, recall 83%
- SVM: accuracy 83%, recall 82%

이는 앞서, 스케일링을 적용한 기존 연속형 변수들에 age 피처를 범주화 및 인코딩 과정에 포함시키고, Wt, Ht 피처들을 포함하여 모델링을 했을 때의 결과보다도 더 좋은 예측 성능을 보여준다.

### 변수 중요도 확인

**RandomForest의 Feature Importances**

<p align="center"><img src="https://user-images.githubusercontent.com/38115693/147505056-2387a2e8-3405-4b62-baf3-ca0748616b6d.png" width="50%" height=""></p>

RandomForest의 Feature Importances로 변수 중요도 확인 결과, FBG, HbA1c, GGT, ALP, BMI, Wt 등이 당뇨병 진단 예측에 높은 중요도를 가지는 것으로 나타났다.

하지만 RandomForest의 Feature Importances는 다소 편향성(bias)이 있는 것으로 알려져 있다. 특히, RandomForest는 연속형 변수 또는 카테고리 개수가 매우 많은 변수, 즉 ‘high cardinality’ 변수들의 중요도를 더욱 부풀릴 가능성이 높다고 한다. 'Cardinality'가 큰 변수일 수록, 노드를 나눌 것이 훨씬 더 많아서 노드 중요도 값이 높게 나오는 것으로 추측된다. 또한, 이 불순도를 기반으로 한 변수 중요도는 train 과정에서 얻은 중요도이기 때문에, test 데이터셋에서는 이 변수 중요도가 어떻게 변하는지 알 수 없다. 실제 test 데이터셋에서는 중요하지 않은 변수가 train 과정에서는 중요한 변수로 계산 될 수 있다. 따라서, RandomForest의 Feature Importances 외에 Permutation Feature Importance와 같은 다른 방법을 혼합해서 사용하는 것이 좋다.

**Permutation Importance**

Permutation Importance는 모델 예측에 가장 큰 영향을 미치는 피처를 파악하는 방법으로 훈련된 모델이 특정 피처를 사용하지 않았을 때 이것이 성능 손실에 얼마만큼의 영향을 주는지를 통해 그 피처의 중요도를 파악한다. 특히, Permutation Importance는 모델 훈련이 끝난 뒤에 계산 되기에 모델을 재학습 시킬 필요가 없으며, 어떤 모델이든 적용할 수 있는 것으로 알려져 있다.

<p align="center"><img src="https://user-images.githubusercontent.com/38115693/147505301-bdd4fd36-1e3f-4523-8b52-c6fcfbdec4de.png" width="50%" height=""></p>

Permutation Importance로 당뇨병 진단 예측에 있어 변수 중요도 확인 결과, FBG, HbA1c, BMI, age, Wt, gender_F, ALP, GGT 등의 순서로 변수 중요도가 높다. 이 중, FBG, HbA1c, age, GGT, ALP 피처들은 모두 회귀분석 결과 통계적으로 유의했던 변수들이기도 하다.

변수 중요도 결과들을 종합하면, 당뇨병 진단에 있어 공복시혈당(FBG), 당화혈색소(HbA1c), 체질량지수(BMI), 몸무게(Wt), 나이(age), 감마글루타밀전이효소(GGT), 알칼라인산분해효소(ALP) 수치가 중요한, 유의한 연관성이 있는 변수인 것으로 보인다. 그렇다면, 당뇨병 진단 리스크에 대한 사전 예측과 이러한 수치들에 대한 적절한 관리를 통해 당뇨병을 예방하고 코로나19에 대한 취약성 또한 감소시킬 수 있을 것이라 생각한다.

당뇨병 치료에 새로운 가능성을 열어준 인슐린이 발견된지 100년의 시간이 흘렀지만, 여전히 당뇨병에 대한 두려움은 크다. 고령화가 심화되면서 당뇨병 환자의 수는 증가하고 있고, 코로나19로 인해 당뇨병에 대한 위험 요인 또한 늘어났으며, 무엇보다 당뇨병 환자들은 큰 위협을 받고 있다. 그러니 이 코로나 시대에, 모두 경각심을 갖고 당뇨병 리스크를 사전에 파악하고, 이에 따른 당뇨병 자가 관리를 해 나가는 것이 그 어느 때 보다 필요하다.

---

## 4. 프로젝트 한계 및 과제
### 한계점
- 모델을 충분히 학습시키고 더 좋은 성과를 내기에는 데이터가 조금 부족 했었습니다. 만약 시간과 데이터가 더 주어진다면, 더 좋은 모형을 만들어 정확한 예측을 할 수 있을 거라 생각합니다. 
- 데이터에 대한 설명이 부족했습니다. 최대한 정보를 모아 데이터를 이해해 모델을 학습시켰지만, 아쉽게도 버려야 하는 컬럼들이 존재했습니다. 활용하지 못한 변수도 포함해 적용해 본다면, 더 나은 성과를 내지 않았을까 생각됩니다.
- 의료 데이터에 대한 지식이 부족해 간단한 관계들만 확인 해 보았다는 것이 아쉬움이 큽니다. 
- 각 수치들이 학회 및 연구 별로 다양한 방법으로 범주화를 적용하고 있습니다. 우리는 그 중 하나의 방법으로만 적용해 보았는데, 다양한 기준점을 갖고 값을 그룹화 했었으면 어땠을 까하는 아쉬움이 남았습니다.

### 향후 과제
- 각 컬럼별로 성별과 연령대로 나눠 범주화 한 기준들이 존재했습니다. 이를 적용해 값들을 세분화하여 적용해 보면 좋을 것 같습니다.
- 불균형 데이터를 다루는 방법에 대해 미숙했습니다. 또한, 범주형과 연속형 변수들이 섞여 있는 데이터에 대해 사용할 수 있는 기법들이 존재했는데 이를 적용해보며 성능이 향상 되는지를 확인할 필요가 있어보입니다.

---
## 5. 참고 문헌
- [고려대학교구로병원 진단검사의학과](http://guro.kumc.or.kr/dept/main/index.do?DP_CODE=GRCP)
- [대한고혈압학회](http://www.koreanhypertension.org/)
- [대한비만학회](https://www.kosso.or.kr/)
- [서울신경내과](http://seoulnim.com/news/lecture_v.asp?srno=7628)
- [서울아산병원.질환백과](https://www.amc.seoul.kr/asan/main.do)
- [순천향대부천병원 진단검사의학과](https://www.schlab.org/guide/)
- [위키백과.당뇨병](https://ko.wikipedia.org/wiki/%EB%8B%B9%EB%87%A8%EB%B3%91)