![IMG_0269](https://user-images.githubusercontent.com/48639017/147466091-97ed72bc-5a28-4258-8616-13fe49549926.jpg)

---

# Contents⛰

1. [프로젝트 소개](#프로젝트-소개)
    - 동기 및 목표
    - 데이터
2. [데이터 전처리](#데이터-전처리)
    - 주요 내용
3. [모델링](#모델링)
    - 모델 선택
    - 모델 성능 평가
4. [모델 예측 결과](#모델-예측-결과)
    - 예측 결과 및 평가
    - 추천 서비스
5. [결론](#결론)
    - 한계점
    - 개선점
---
    

# 프로젝트 소개
### 동기 및 목표
💡 동기
- 코로나로 인해 야외 활동 인구가 증가하며 등산의 인기도 상승
- 전국적으로 100대 병산이 분포되어 있으나 추천 지역은 대체로 서울에 한정

💡 목표
- 100대 명산과 유사한 산 추천

### 데이터
![unknown](https://user-images.githubusercontent.com/48639017/147468962-d33b46ca-88d2-49bd-874e-aa0560f34a97.png)

    

# 데이터 전처리
### 주요 내용
1. 전체 산 하나의 row로 데이터프레임 생성
![1](https://user-images.githubusercontent.com/48639017/147469557-fadf47e7-1c0c-4961-b9d8-44dfd193d338.png)

    - MNTN_CODE : 산 고유 코드
    - MNTN_NM : 산 이름
    - MNTN_LOC : 산 소재지
    - MNTN_HEIGHT : 산 높이
    - ROAD_EASY/MID/DIFF : 난이도 별 등산로 길이
    - ROAD_SUM : 산의 총 등산로 길이
    - MNTN_RES : 산 정보 api 검색 결과
    - Sel_Cos, Raw_Cos : 100대 명산과의 코사인 유사도 값

2. 자연어 처리
    - 100대 명산 api 검색 결과에서 Konlpy - KKMA 활용 명사 추출
    - CountVectorizer 후에 Raw, Selected 말뭉치 추출
        - Raw : 100대 명산 검색 결과의 모든 명사
        - Selected : 검색 결과 중 빈도수 100회 이상이거나 핵심이라고 생각되는 명사
    - Cosine Similarity 계산
        - 말뭉치 벡터화 - OneHot Encoding
        - 말뭉치 벡터와 모든 산 데이터 유사도 측정
3. Train, Test data set
    - Train : 100대 명산 + 산으로 분류하지 않은 데이터
        - 라벨 불균형 해결 - SMOTE
    
    ![2](https://user-images.githubusercontent.com/48639017/147471604-d7a4bd9f-3380-4622-9f10-fe707c4e1482.png)

    - Test : Train을 제외한 나머지 데이터
    - StandardScaler 적용
    
    ![](https://cdn.discordapp.com/attachments/900310026793652244/925002953985773618/unknown.png)
    

# 모델링
### 모델 선택
- 파이프라인 구축 및 최적의 모델 도출
    - 파이프라인 GridSearchCV 거쳐 총 5가지 classifier 모델 중 선택
    - RandaomForestClassifier 선택
    
    <img width="523" alt="Screen Shot 2021-12-28 at 10 12 02 AM" src="https://user-images.githubusercontent.com/48639017/147517136-64a5a368-8ae5-4221-9e9f-75f5b68d881f.png">

### 모델 성능 평가
- K-fold cross validation 하여 성능 평가
    - accuracy, precision, recall
    
    ![score](https://user-images.githubusercontent.com/48639017/147516963-c8f51986-18d6-4890-b1b7-db25098642c8.png)

# 모델 예측 결과
### 예측 결과
- train-test class scatter
    ![res](https://user-images.githubusercontent.com/48639017/147474227-590f26c6-4fa9-461a-925b-d01ed3feed71.png)
- test 데이터 셋 중 좋은 산으로 분류된 산 총 493개

### 예측 평가
- 좋게 분류된 493개 중 블랙야크 알파인 클럽 100+ 명산 55개 포함
- 좋게 분류된 493개 중 랜덤 추출 후 정성적 평가

### 💡 추천 서비스
- 사용자 희망 지역 선택 후 좋은 산 top 5 추천
    - Class 1로 예측할 proba 값 기준 높은 순으로 추천

    <img src = https://user-images.githubusercontent.com/57916633/147496793-014c86a8-3b80-46d4-81ce-45b36587e7ea.png width = 250 height = 400>


    
# 결론
### 한계점
- open Api
    - 통일되지 않은채 관리되고, 모든 데이터를 포함하지 않음
- 정성적 평가 방법
    - 모델 성능 비교 불가능

### 개선점
- 자연어 처리
    - onehot-encoding의 한계점으로 문장간 유사도 파악 어려움
    - tf-idf 활용 후 유사도 도출로 개선 가능
    - Sel_Cos 유무에 다른 예측 분포 차이 원인 파악
    
    ![compare](https://user-images.githubusercontent.com/48639017/147475344-bfa67056-155a-4494-8e98-8ab0e3796155.png)




---

### 참고
[등산로 정보 데이터](https://www.forest.go.kr/kfsweb/kfi/kfs/trail/trailInformation.do?pblicDataId=PBD0000041&tabs=3&mn=NKFS_06_08_02&subTitle=%EB%93%B1%EC%82%B0%EB%A1%9C%EC%A0%95%EB%B3%B4)

[명산 등산로 데이터](https://www.forest.go.kr/kfsweb/kfi/kfs/nwopapi/gdTrailInformation.do?pblicDataId=PBD0000021&tabs=3&mn=NKFS_06_08_02&subTitle=%EB%AA%85%EC%82%B0%EB%93%B1%EC%82%B0%EB%A1%9C)

[산정보 데이터](https://www.forest.go.kr/kfsweb/kfi/kfs/nwopapi/gdTrailInformation.do?pblicDataId=PBD0000021&tabs=3&mn=NKFS_06_08_02&subTitle=%EB%AA%85%EC%82%B0%EB%93%B1%EC%82%B0%EB%A1%9C)

---

### 개발환경 💻
- Python >= 3.8 version
- Scikit-learn >= 0.24 version
- Konlpy == 0.5.2 version


---

### 사용 기술 스택 🛠️
- Python
- Scikit-learn
- Konlpy
- Pandas
- Numpy
- Git

<img src = https://user-images.githubusercontent.com/57916633/147495075-d4e1c132-522c-45dc-aedf-81cd25d8530c.png width="950" height="350"/>

