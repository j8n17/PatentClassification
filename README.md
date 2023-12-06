# PatentClassification - 5조
### 팀프로젝트
- 주제 : 특허 문서 기반 특허 분류

- 목표 : 문서의 text를 분석하여 적합한 분류 번호(SSno)를 출력하는 모델 생성

- 기술적 목표 : 전처리 및 적합한 모델 선정으로 특허 분류의 정확도 향상
  1. text 전처리 - 기호, 이미지 text, 영어, 한자 etc...
  2. model - `koelectra` , `KorPatELECTRA` 성능 비교
     * KorPatELECTRA는 한국특허정보원에서 국내특허문언 text를 pretraining 한 모델
---------------

## 파일 구조
```
data
├─  train
│   ├─  docs
│   └─  labels
├─  test
│   └─  test_input.csv
└─  sample_submission.csv

baseline
├─  config
│   └─  preprocess.yaml, train.yaml, pred.yaml
├─  modules
│   └─  loss.py
├─  preprocess.py, train.py, pred.py
├─  Readme.md
│ 
└─  results
    └─  checkpoint-#####
        └─  submission.csv
```
- config: 전처리/학습/추론 hyperparameter 설정
  - train.yaml
  - pred.yaml
  - preprocess.yaml

- preprocess.py: 전처리 코드. `train.csv`, `category.csv` 파일 생성
- train.py: 학습 코드. `./results/checkpoint-#####` , `runs` 생성
- predict.py: 추론 코드. `./results/checkpoint-#####/submission.csv` 생성

--------------------
### 하이퍼 파라미터
- train.yaml
  - 실험
      - lr_scheduler_type: `constant_with_warmup` or `cosine_with_restarts`
      - learning_rate: `5e-5`~`8e-4`
      - epoch: `10`~`100`
      - load_best_model_at_end=True
  - 실험 모니터링
    - report_to: ['tensorboard']
    - save_strategy: 'steps'
    - save_steps: 890
    - logging_steps: 89
  
- pred.yaml 
  - threshold: 1.5~2.0
 
### 실험 진행
1. 전처리 파트:
   ## 1차 전처리 데이터 셋:
     1. 기호, 영어, 한자 제거
     2. mecab으로 형태소 분리 (한국특허정보원에서 제공한 mecab dictionary 적용)
     3. 유의미한 문자열이라고 판단 가능한 품사 NNG만을 남기고 제거.
          
   ## 2차 전처리 데이터셋 :
     1. html 문서 고유의 문자 제거 ex) &amp , &lt , \<claim-text/\>
     2. 한국특허정보원에서 제공하는 vocab.txt 파일 참고하여 대체 가능한 단어로 변경 및 통일
     3. 괄호로 감싸진 문자 제거 : (10)과 같이 주로 단어를 숫자로 라벨링한 것이 대부분을 차지함. 일부 부연설명과 한글을 영어,한자로 변경한 내용을 포함하고 있지만 제거가 큰 영향을 주지 않을 것이라 판단함.
     5. 수식 제거
     6. 특수기호를 포함한 문자열을 `모양` 단어로 대체 : `ex) '┳ 형상' -> '모양'

3. 모델 파트:
   ## koelectra
      1. 
   ## KorPatELECTRA
