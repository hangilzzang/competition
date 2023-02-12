[대회링크](https://dacon.io/competitions/official/236027/leaderboard)

# 최종등수: 512명중 12등

![image](https://user-images.githubusercontent.com/104988924/208335767-c6495d04-d8f6-45fc-87ef-4bfa56da4678.png)


## 개발환경: 코랩
타겟의 종류는 7가지이며 분포는 불균형합니다
<p align="left">
<img width="447" alt="다운로드" src="https://user-images.githubusercontent.com/104988924/208293054-f8ce2a3a-3fcd-4efb-9062-3d9ba19caad1.png">

train의 speaker와 test의 speaker는 동일인물이 한명도 없었기에 라벨인코딩을거친 feature로써는 사용할수없었습니다

Dialogue_ID의 경우 같은 대화인경우 같은 숫자를 가집니다

원본 데이터의 경우는 아래와같이 구성되어있습니다

![image](https://user-images.githubusercontent.com/104988924/208292945-c81a9f98-d948-49b6-b2b9-1c77051b56f9.png)

pretrained model로는 [emoberta-large](https://huggingface.co/tae898/emoberta-large)모델을 활용했습니다

특이점이라면 emoberta-large이 자체적으로 분류하는 감정의 종류가 Target과 정확히 일치합니다

여러가지모델로 fine tuning을 시도해보았지만

fine tuning을 하지않고 emoberta-large모델 그자체로만 test 데이터를 예측한것이 가장 점수가 잘나왔습니다

# 머신러닝 방법론 접근
emoberta-large로 train과 test를 예측했습니다 

![image](https://user-images.githubusercontent.com/104988924/208303642-5e8a029d-7dca-4a08-a5ff-b254c536d6f1.png)

## 파생변수 생성
### sumed_Utterance
  
![无标题](https://user-images.githubusercontent.com/104988924/208305480-8ed0a474-1465-409f-8ac8-1e0eaf249631.png)
  
같은 speaker가 말한 문장이 같은대화속에서 연달아 나올때 같은 감정으로 나오는 경우가 많다는걸 발견했습니다

발화자가 같은 문장이 연속으로 나올경우 합치는 과정을 진행했고 sumed_Utterance로 이름지었습니다

### Dialogue_length

각 dialogue의 길이를 나타냅니다
  
### Utterance_length
각 문장의 단어 개수를 나타냅니다

### next, previous
같은 Dialogue안에서의 다음또는 전 문장의 감정정보를 가져옵니다

현재 발화자의 감정은 전,후 발화자의 감정에 영향을 받는다고 생각해 가져왔습니다

결측값은 모두 natural감정으로 채웠을때가 가장 결과가 좋았습니다
  
### ranking
1위부터 7위까지 감정컬럼들의 예측값을 비교해 순위로 매겼습니다

![image](https://user-images.githubusercontent.com/104988924/208334921-ece2a8ea-0646-46e6-b496-f7d46bdf3b73.png)

![image](https://user-images.githubusercontent.com/104988924/208335027-c9c70ff0-068c-4c59-9003-2e1411ae8771.png)

이후에 KFold를 이용한 lgbm 모델 파라미터 튜닝을 진행하여 마무리 지었습니다
