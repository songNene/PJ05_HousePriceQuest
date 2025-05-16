# 🏠 House Prices - Advanced Regression Techniques

본 프로젝트는 Kaggle의 [House Prices - Advanced Regression Techniques](https://www.kaggle.com/competitions/house-prices-advanced-regression-techniques) 대회 데이터를 기반으로 주택의 가격을 예측하는 회귀 모델을 구축한 과정입니다.

---

## 🔧 프로젝트 개요

- **목표**: Ames 주택 데이터를 기반으로 `SalePrice`를 예측
- **접근 방식**:
  - 데이터 정제 및 결측치 처리
  - 왜도(Skew)가 높은 수치형 피처에 로그 변환 적용
  - 범주형 피처에 One-Hot Encoding 적용
  - 이상치 제거
  - XGBoost, LightGBM, Lasso, Ridge 모델 학습 및 하이퍼파라미터 튜닝
  - 앙상블 평균을 통해 최종 예측 수행

---

## 📈 데이터 전처리 주요 내용

- **로그 변환**: `SalePrice`와 `GrLivArea`, `1stFlrSF`, `2ndFlrSF`, `TotalBsmtSF`에 `log1p` 변환 적용하여 정규성 확보
- **결측치 처리**:
  - `PoolQC`, `MiscFeature`: 각각 `'NoPool'`, `'NoMisc'`로 결측 대체
  - `FireplaceQu`, `Garage*`, `Bsmt*` 등: 도메인 지식을 활용하여 일괄 치환
  - `LotFrontage`: `Neighborhood` 기준으로 중앙값으로 대체
- **범주형 처리**: `get_dummies`로 One-Hot Encoding 수행
- **이상치 제거**:
  - `GrLivArea > 4000 and SalePrice < 300000` 조건의 이상치
  - `GarageArea` 상위 IQR 초과값 제거

---

## 🤖 모델 및 성능 평가

- 사용한 회귀 모델:
  - XGBoost
  - LightGBM
  - Lasso Regression
  - Ridge Regression
- 모델 성능 평가 지표:  
  - RMSE (Root Mean Squared Error)  
  - MAE (Mean Absolute Error)  
  - RMSLE (Root Mean Squared Log Error)

### 🔀 앙상블
- `XGBoost(40%) + Lasso(30%) + Ridge(30%)` 조합
- 앙상블 성능 향상에 기여하며 일반화 능력 보완

---

## 🔄 회고: 반복과 실험의 중요성

### 1. 전처리 우선주의의 함정
초기에는 가능한 많은 전처리를 한 번에 끝내고 모델을 학습하는 방식으로 접근했다.  
그러나 결과적으로, 이렇게 과도한 전처리를 단번에 하는 방식은 효과적이지 않았다.  
👉 오히려 **기본적인 전처리를 먼저 수행한 후**, 모델을 학습해보고  
**모델 성능을 확인하면서 점진적으로 전처리를 개선해 나가는 방식**이 더 유의미했다.

### 2. 캐글 점수의 오차 원인 분석
로컬에서는 모델 성능이 매우 안정적이었음에도, 캐글 제출 점수에서는 9만 달러 이상의 큰 오차가 발생했다.  
한참 원인을 고민한 끝에, **고가 주택 예측력이 낮았다는 점**을 발견했고,  
이는 데이터 전처리 과정에서 고급 주택의 특성을 반영하는 중요한 피처들이 삭제되었기 때문이었다.  
이후 `PoolQC`, `MiscFeature` 등을 복구하고 적절히 처리하면서 점수를 개선할 수 있었다.

---

## 📁 최종 제출

- `submission.csv`: 앙상블 결과를 `expm1`로 역변환하여 제출
- 캐글 점수 기준: RMSLE 기준 평가

---

## 🧠 배운 점

- 데이터 전처리는 "많이"보다 "의미 있게" 하는 것이 중요하다
- 모델링은 탐색과 실험의 연속이다: 작은 변경도 큰 차이를 만든다
- 희귀 피처의 의미를 버리지 않고 살리는 것이 예측 성능 향상에 도움이 된다
