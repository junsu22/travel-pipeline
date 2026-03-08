# team_project_travel_pipeline

고객 정보를 바탕으로 여행 상품 구매 여부를 예측하는 머신러닝 파이프라인 프로젝트입니다.  
데이터 로딩부터 전처리, 피처 엔지니어링, 모델 비교, 하이퍼파라미터 튜닝, 모델 평가까지의 과정을 단계별로 정리했습니다.

---

## 1. 프로젝트 개요

본 프로젝트는 여행 상품 구매 여부(`ProdTaken`)를 예측하는 이진 분류 머신러닝 파이프라인을 구현한 팀 프로젝트입니다.  
고객의 인구통계 정보, 상담 정보, 상품 관련 정보 등을 활용하여 구매 가능성을 예측하고,  
여러 모델을 비교한 뒤 최종적으로 성능이 가장 균형 잡힌 모델을 선택했습니다.

---

## 2. 데이터 출처

- Kaggle: Holiday Package Purchase Prediction  
- 링크: https://www.kaggle.com/datasets/susant4learning/holiday-package-purchase-prediction

---

## 3. 팀원

- 김준수: 베이스라인 구현, 전처리/파이프라인 리팩토링

- 김한별: LightGBM, XGBoost 실험 및 시각화

- 서동열: XGBoost, 로지스틱 회귀 실험, 데이터 수집 및 정리

XGBoost도 실험 대상에 포함해 테스트했지만, 
최종 발표에서는 비교 구조를 더 단순하고 명확하게 정리하기 위해 리팩토링 과정에서 제외했습니다.

---

## 4. 프로젝트 목표

- 고객 정보를 바탕으로 여행 상품 구매 여부를 예측
- 데이터 전처리와 피처 엔지니어링 과정을 파이프라인으로 구성
- 여러 분류 모델 성능 비교
- 하이퍼파라미터 튜닝을 통한 성능 개선
- 최종 모델 해석 및 저장

---

## 5. 데이터 설명

원본 데이터는 고객의 나이, 월소득, 직업, 상담 정보, 상품 제안 정보 등으로 구성되어 있습니다.  

- 타깃 변수: `ProdTaken`
  - `0` = 미구매
  - `1` = 구매

### 주요 변수 예시
- `Age` : 나이
- `MonthlyIncome` : 월소득
- `Occupation` : 직업
- `DurationOfPitch` : 상품 설명 시간
- `NumberOfTrips` : 여행 횟수
- `Passport` : 여권 보유 여부

---

## 6. 프로젝트 진행 과정

### 1) 데이터 로딩
- CSV 파일 불러오기
- 데이터 크기 및 샘플 확인

### 2) EDA(탐색적 데이터 분석)
- 결측치 개수 확인
- 구매 여부 분포 확인
- 직업별 구매율 확인

### 3) 전처리 및 피처 엔지니어링
- 결측치 처리
- 숫자형 / 범주형 컬럼 분리
- 표준화(StandardScaler)
- 원-핫 인코딩(OneHotEncoder)
- 파생 변수 생성
  - `TotalVisitors` : 총 방문 인원 수
  - `HighIncome` : 월소득이 중앙값 이상이면 1, 아니면 0

### 4) 모델 학습 및 비교
비교한 모델은 다음과 같습니다.

- 로지스틱 회귀(Logistic Regression)
- 랜덤 포레스트(Random Forest)
- LightGBM

### 5) 하이퍼파라미터 튜닝
- `RandomizedSearchCV`를 사용하여 LightGBM 튜닝
- ROC-AUC 기준으로 최적 파라미터 탐색

### 6) 모델 평가
다음 지표를 사용하여 성능을 비교했습니다.

- Accuracy(정확도)
- Precision(정밀도)
- Recall(재현율)
- F1 Score
- ROC-AUC

### 7) 최종 모델 해석
- 혼동행렬(Confusion Matrix) 확인
- 변수 중요도(Feature Importance) 분석

### 8) 모델 저장
- `pickle`을 사용해 최종 모델 저장 및 불러오기 확인

---

## 7. 전처리 파이프라인

숫자형과 범주형 변수는 전처리 방법이 다르기 때문에 각각 분리하여 처리했습니다.

- 숫자형 데이터
  - 결측치 → 중앙값 대체
  - 표준화

- 범주형 데이터
  - 결측치 → 최빈값 대체
  - 원-핫 인코딩

---

## 8. 하이퍼파라미터 튜닝

최종 후보 모델인 LightGBM에 대해 `RandomizedSearchCV`를 적용했습니다.

### 탐색한 파라미터
- `n_estimators`
- `learning_rate`
- `num_leaves`

### 튜닝 결과
- 튜닝 후 모델은 Accuracy, F1 Score, ROC-AUC 측면에서 전반적으로 개선된 성능을 보였습니다.
- 최종적으로 **LightGBM (Tuned)** 을 최종 모델로 선택했습니다.

---

## 9. 주요 결과 요약

- 랜덤 포레스트는 ROC-AUC가 가장 높았지만, Recall이 낮아 실제 구매 고객을 놓칠 가능성이 있었습니다.
- 튜닝된 LightGBM은 Accuracy, F1 Score, Recall이 전반적으로 더 균형 잡힌 성능을 보였습니다.
- 따라서 최종 모델은 **LightGBM (Tuned)** 으로 선정했습니다.

---

## 10. 변수 중요도 해석

최종 모델의 변수 중요도 분석 결과,

- `MonthlyIncome(월소득)`
- `Age(나이)`
- `DurationOfPitch(상품 설명 시간)`

등이 중요한 변수로 나타났습니다.

특히 `MonthlyIncome`이 가장 높은 중요도를 보여,  
고객의 월소득이 여행 상품 구매 여부 예측에 큰 영향을 주는 변수임을 확인했습니다.

---

## 11. 직업별 구매율 해석 시 주의점

직업별 구매율 그래프에서 `Free Lancer`의 구매율이 100%로 나타났지만,  
이는 해당 직업군의 표본 수가 2명뿐이고 그 2명이 모두 구매했기 때문입니다.  

따라서 이 결과는 일반화하기보다 **참고용으로 해석**해야 합니다.

---

## 12. 실행 환경

- Python
- Pandas
- NumPy
- Scikit-learn
- LightGBM
- Plotly
- Pickle

---

## 13. 파일 구성 예시

```bash
team_project_pipeline_final/
│
├── README.md
├── travel_pipeline_final.ipynb
├── travel_pipeline_final.py
└── data/
    └── Travel.csv
