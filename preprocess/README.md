# Preprocess 관련 설명

먼저 find_special_0.ipynb를 통해 학습 데이터에 어떤 특수기호가 있는지 파악하고 각 항목 'invention_title', 'abstract', 'claims'에서 해당 특수기호들을 찾아서 저장한다.

그 다음, preprocess_labeling_1.ipynb을 통해 이 파일에 있는 코드는 interactive 기능이 있는 ipywidgets을 이용한 코드로 직접 특수기호가 있는 데이터들을 보면서 해당 특수기호를 어떻게 처리할 지 정할 수 있다.

<img width="500" alt="스크린샷 2023-12-01 17 18 36" src="https://github.com/j8n17/PatentClassification/assets/85532197/e1b0416c-4027-4070-8790-5ca6aa32762c">

이때 <제목>, a<b와 같이 다양한 의미로 사용되는 특수 기호의 경우, 섣불리 정하기 어려우므로 '보류'로 등록한다.

마지막으로 '보류'처리된 데이터들은 check_undetermined_2.ipynb를 통해 좀 더 다양한 텍스트들을 보면서 해당 특수기호를 어떻게 처리할 지 정할 수 있다.

이렇게 구축된 전처리 사전을 이용해 preprocess.ipynb에서는 학습 데이터를 전처리할 수 있다.

전처리 된 학습데이터는 데이터 불균형 해소를 위해 data_balancing.ipynb에서 업샘플링을 한다.
