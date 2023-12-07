# PatentClassification - 5조
팀장 : 김준

팀원 : 김현동 , 두현영, 이성민 , 조유원

기간 : 2023.10.15 ~ 2023.12.07

### :european_post_office: 팀프로젝트
- 주제 : 특허 문서 기반 특허 분류

- 목표 : 문서의 text를 분석하여 적합한 분류 번호(SSno)를 출력하는 모델 생성

- 기술적 목표 : 전처리 및 적합한 모델 선정으로 특허 분류의 정확도 향상
  1. text 전처리 - 기호, 이미지 text, 영어, 한자 etc...
  2. model - `koelectra` , `KorPatELECTRA` 성능 비교
     * KorPatELECTRA는 한국특허정보원에서 국내특허문언 text를 pretraining 한 모델
---------------

## :memo: 파일 구조
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
  - threshold: 가중치 값을 직접 기입하는 것이 아닌 머신 예측확률에 의한 최종결정을 위함
    ```
    w=0.99
    preds = (logits >= w*logits.max(axis=1).reshape(-1, 1))
    ```
------------------------------

## 👨‍💻 실험
### 개발 환경
  - colab
  - GPU : T4 , V100
  - pytorch version
  - python version
  - extra tool : notion , google mee
  
    
### 전처리 파트
   #### text 전처리 과정
     1. html 문서 고유의 문자 제거 `ex) &amp , &lt , \<claim-text/\>`
     2. 특수기호를 포함한 문자열을 `모양` 단어로 대체 `ex) '┳ 형상' -> '모양'
     3. 한국특허정보원에서 제공하는 vocab.txt 파일 참고하여 대체 가능한 단어로 변경 및 통일 `ex) 특수문자 -> 영어 , 범위 문자 -> () [특수문자 예시], link ` 
     4. 괄호로 감싸진 문자 제거 : (10)과 같이 주로 단어를 숫자로 라벨링한 것이 대부분을 차지함. 일부 부연설명과 한글을 영어,한자로 변경한 내용을 포함.
     5. 수식 제거 `ex) C + Mn/6 + (Cr + Mo + V)/5 + (Cu+Ni)/15`
     6. 일부 특수기호 한글로 대체
     7. 띄어쓰기 없는 text 처리 (mecab)
     8. 18자 이상 연속되는 text 제거 (vocab text 최대 글자길이 17자 기준)
     9. 남은 모든 특수기호 제거
     10. mecab 형태소 분리  (한국특허정보원에서 제공하는 dictionary 적용)
     

### 모델 파트
  #### 파이썬 파일 실행 명령어 및 결과
  1-0. 모듈을 불러오기 위한 설치 진행.

    !pip install requirements
    
  1-1. 모델 학습 : results 폴더 생성. 학습된 모델 정보 저장.
  
    !python train.py
  
  1-2. 추가 학습 진행 : 이전에 학습된 모델 정보를 읽어서 추가 학습 가능.
  
    !python train.py --checkpoint_path "./results/checkpoint-####

  2. 학습된 모델로 예측
    
    !python pred.py --pretrained_model_name_or_path "./results/checkpoint-###" --submission_csv "./results/checkpoint-###"
  
  #### 실험
  ```
  1. 한국특허정보원에 pre-training 모델 신청 및 코드 적용.
  2. 적절한 lr 찾기 : text 전처리 하기 전 데이터로 실험. 학습시간, 학습률, tensorboard 모니터링 참고
  3. 전처리 한 데이터에 lr 적용하여 학습.

  품사 only NNG 활용 데이터 셋(한국특허정보원에서 제공하는 dictionary 적용) 
  1. 기호, 영어, 한자 제거
  2. mecab으로 형태소 분리 (한국특허정보원에서 제공한 mecab dictionary 적용)
  3. 유의미한 문자열이라고 판단 가능한 품사 NNG만을 남기고 제거
  ```

![Untitled](https://github.com/j8n17/PatentClassification/assets/100841549/e3473722-3bff-4653-b3fd-2af1f8fb1891)

참조 : https://github.com/kipi-ai/korpatelectra
