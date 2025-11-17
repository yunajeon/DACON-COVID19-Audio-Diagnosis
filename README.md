# DACON: COVID-19 음성 데이터 검출 AI 경진대회 (최종 22위)



[DACON: 월간 데이콘 음향 데이터 COVID-19 검출 AI 경진대회](https://dacon.io/competitions/official/235905/overview/description)에 참여하여 600명 이상의 참가자 중 **최종 22위 (상위 10% 이내)**를 달성한 프로젝트입니다.

---

## 1. 프로젝트 개요

COVID-19 검출을 위한 표준 방법인 RT-PCR 검사는 비용이 많이 들고 시간이 오래 걸린다는 한계가 있습니다. [cite: 133] [cite_start]특히, 바이러스 재유행으로 인해 검사량이 급증할 경우 의료 체계에 큰 부담이 될 수 있습니다.

본 프로젝트는 COVID-19의 주요 증상 중 하나인 **'기침 소리'**에 주목했습니다. AI 기술을 활용하여 음향 데이터(기침 소리)로부터 COVID-19 감염 여부를 판별하는 모델을 개발하여, 기존 진단 도구의 한계점을 보완할 수 있는 빠르고 효율적인 진단 도구를 설계하는 것을 목표로 합니다.

* **프로젝트 기간:** 2022.05.31 - 2022.07.08 
* **평가 지표:** Macro F1 Score 

---

## 2. 나의 주요 역할 (My Contribution)

본 프로젝트에서 데이터 전처리 및 모델 최적화 파트를 담당했습니다.

* **음성 피처 엔지니어링:** `Librosa` 라이브러리를 활용하여 핵심 피처인 MFCC (Mel-Frequency Cepstral Coefficients) 추출 
* **오디오 전처리:** `librosa.effects.trim`을 이용한 무음 구간 제거 및 오디오 길이를 4.09초로 통일하여 피처 차원 정규화
* **모델 최적화:** `RandomizedSearchCV`를 활용한 `MLPClassifier` 하이퍼파라미터 튜닝 

---

## 3. 개발 과정 (Process)

제출한 `.ipynb` 파일의 코드 흐름 기준입니다.

1.  **데이터 로드:** `train_data.csv`, `test_data.csv` 및 개별 `.wav` 음성 파일 로드
2.  **피처 추출 (Feature Extraction):**
    * `librosa.load`로 16000Hz(SR)로 샘플링하여 오디오 로드
    * `librosa.effects.trim(top_db=40)`을 적용하여 음성 앞뒤의 유의미하지 않은 무음 구간(Silence) 제거
    * 오디오 길이를 4.09초로 고정 (`chunk = 4.09`). 이보다 길면 자르고, 짧으면 0으로 패딩(Zero-padding)하여 모든 데이터의 피처 크기를 통일
    * `librosa.feature.mfcc(n_mfcc=32)`를 적용하여 32차원의 MFCC 피처 추출
    * 추출된 2D MFCC 피처의 각 행(MFCC 계수)별 평균(`np.mean`)을 계산하여 1D 벡터(32차원) 생성
3.  **데이터셋 구성:**
    * 기존 `train_data.csv`의 정보(성별, 연령 등)와 추출된 32차원 MFCC 피처를 결합하여 `train_mfcc_data3.csv` 생성
    * 'gender' 피처를 `OneHotEncoder`로 인코딩
4.  **모델링 및 최적화:**
    * `train_test_split`을 사용해 학습(70%) / 검증(30%) 데이터 분리 (`random_state=1200`)
    * [cite_start]`MLPClassifier` (다층 퍼셉트론) 모델을 선정 [cite: 211]
    * `GridSearchCV`를 사용해 최적의 하이퍼파라미터 탐색 (`cv=5`, `scoring='f1_macro'`)
    * **최적 하이퍼파라미터:**
        * `activation`: 'relu'
        * `alpha`: 0.01
        * `batch_size`: 'auto'
        * `hidden_layer_sizes`: 65
        * `learning_rate_init`: 0.01
        * `solver`: 'adam'
5.  **제출:** 최적화된 모델(`clf_nn_opt`)로 `test_x` 데이터를 예측하여 `submit6.csv` 파일 생성

---

## 4. 최종 결과

* **최종 리더보드 순위:** **22위** (상위 10% 이내) [cite: 421, 424]
* [cite_start]**최종 리더보드 점수 (Public Score):** **0.5936** [cite: 427]
* **로컬 검증 점수 (Local F1 Score):** **0.6056**

---

## 5. 사용 기술 (Tech Stack)

* **Data Handling:** `Python`, `Pandas`, `NumPy`
* **Audio Processing:** `Librosa`
* **Machine Learning:** `Scikit-learn`
    * [cite_start]Model: `MLPClassifier` [cite: 211]
    * Preprocessing: `OneHotEncoder`, `train_test_split`
    * [cite_start]Tuning: `GridSearchCV`, `RandomizedSearchCV` [cite: 351]
* **Visualization:** `Matplotlib`, `librosa.display`
* **Utility:** `tqdm`

---

## 6. 레포지토리 구조

```
/DACON_COVID19_Audio_Diagnosis/
│
├── .gitignore
├── README.md               # (현재 이 파일)
├── requirements.txt        # (프로젝트 필요 라이브러리)
│
├── code/
│   └── COVID19_Audio_Diagnosis.ipynb  # (제출한 메인 노트북)
│
└── docs/
    └── COVID19_Project_Presentation.pdf # (프로젝트 상세 발표 자료)
```

---

## 7. 📄 프로젝트 상세 자료 (PDF)

본 프로젝트의 상세한 개발 과정, MFCC 피처 추출 방법, 모델 최적화 결과가 포함된 발표 자료는 아래 링크에서 확인하실 수 있습니다. (PDF 파일을 `docs/` 폴더에 업로드해야 링크가 활성화됩니다.)

**[➡️ 프로젝트 상세 발표 자료 (PDF) 보기](./docs/COVID19_Project_Presentation.pdf)**
