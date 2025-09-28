# 🏢 2025 건물 전력 사용량 예측 AI 경진대회

건물별 전력 사용량 및 기상 데이터를 활용하여 미래 전력 사용량을 예측하는 AI 모델을 개발하는 프로젝트입니다. 본 대회는 안정적인 에너지 공급 및 효율적인 수요 관리를 위한 예측 알고리즘 발굴을 목표로 합니다.

---

### ** 주요 특징**

- **계층적 앙상블 모델링**: 전체 건물에 대한 **전체 학습 모델**, **건물 유형별모델**, 개별 건물 **Building 모델**을 구축하여 예측 성능을 계층적으로 보강했습니다.
- **상세한 피처 엔지니어링**: 시간(Cyclic), 공휴일, 기상 지수(CDH, THI, WCT) 등 도메인 특성을 반영한 다양한 파생 변수를 생성하여 모델의 예측력을 높였습니다.
- **2-Stage 앙상블**: **XGBoost**와 **LightGBM**을 각각 계층적 모델링에 적용한 후, 두 모델의 예측 결과를 **가중 평균 앙상블**하여 최종 예측의 안정성과 정확도를 확보했습니다.
- **체계적인 이상치 처리**: 각 건물의 데이터 분포를 분석하여 발견된 이상치를 정교하게 제거하여 모델 학습의 견고함을 높였습니다.

---

### **⚙️ 기술 스택**

- **언어**: Python 3.11
- **주요 라이브러리**:
  - `pandas`, `numpy` for Data Handling
  - `scikit-learn` for ML Utilities
  - `xgboost`, `lightgbm` for Modeling
  - `matplotlib`, `seaborn` for Visualization

---

### **📂 프로젝트 구조**

```
power-consumption-prediction/
├── data/                 # 원본 데이터
│   ├── train.csv
│   ├── test.csv
│   └── building_info.csv
├── notebooks/            # 주피터 노트북
│   └── power_prediction_analysis.ipynb
├── .gitignore            # Git 추적 제외 파일 목록
├── README.md             # 프로젝트 설명서 (본 파일)
└── requirements.txt      # 프로젝트 필요 라이브러리
```

---

### **🚀 실행 방법**

1.  **레포지토리 클론:**
    ```bash
    git clone [https://github.com/your-username/power-consumption-prediction.git](https://github.com/your-username/power-consumption-prediction.git)
    cd power-consumption-prediction
    ```

2.  **가상환경 생성 및 라이브러리 설치:**
    ```bash
    python -m venv venv
    source venv/bin/activate  # Windows: venv\Scripts\activate
    pip install -r requirements.txt
    ```

3.  **데이터 준비:**
    `data` 폴더에 `train.csv`, `test.csv`, `building_info.csv` 파일을 위치시킵니다.

4.  **노트북 실행:**
    `notebooks/power_prediction_analysis.ipynb` 파일을 주피터 노트북 또는 VS Code 환경에서 열고 순서대로 실행합니다. 최종 제출 파일 `ensemble_avg.csv`가 생성됩니다.

---

### **📈 모델링 과정**

#### 1. 데이터 전처리 및 피처 엔지니어링

- **데이터 결합**: `train`, `test` 데이터에 `building_info`를 병합하여 건물 정보를 추가했습니다.
- **이상치 제거**: 특정 건물의 전력 소비 패턴에서 발견된 이상 데이터를 규칙 기반으로 제거했습니다.
- **시간 관련 피처**: `hour`, `day`, `month`, `day_of_week` 등 기본 시간 피처와 시간의 주기성을 반영하는 `sin/cos` 변환 피처를 생성했습니다.
- **공휴일 피처**: 주말, 공휴일 및 특정 건물의 휴무일 규칙을 반영한 `holiday` 피처를 생성했습니다.
- **기상 지수 피처**: 체감온도(WCT), 불쾌지수(THI), 냉방도일(CDH) 등 전력 사용량과 관련이 깊은 복합 기상 지수를 추가했습니다.

#### 2. 계층적 앙상블 모델링 (Hierarchical Ensemble)

예측의 안정성과 정확도를 높이기 위해 세 가지 레벨의 모델을 구축하고 예측 결과를 가중 평균했습니다.

1.  **Global Model (전체 모델)**: 모든 건물 데이터를 사용하여 하나의 모델을 학습합니다. 전체적인 패턴을 학습하는 역할을 합니다.
2.  **Type Model (건물 유형별 모델)**: '학교', '병원' 등 동일한 유형의 건물끼리 그룹화하여 모델을 학습합니다. 유형별 공통 패턴을 학습합니다.
3.  **Building Model (개별 건물 모델)**: 각 건물별로 독립된 모델을 학습합니다. 개별 건물의 고유한 패턴을 세밀하게 학습합니다.

이 세 모델의 예측값을 `0.2:0.4:0.4` 비율로 가중 평균하여 **XGBoost**와 **LightGBM** 각각의 1차 예측값을 생성했습니다.

#### 3. 최종 앙상블

XGBoost와 LightGBM의 계층적 앙상블 예측 결과를 **산술 평균**하여 최종 제출 파일을 생성했습니다.

---

### **📊 결과**

- **평가 지표**: SMAPE (Symmetric Mean Absolute Percentage Error)
- 최종 예측 결과는 `ensemble_avg.csv` 파일로 저장됩니다.