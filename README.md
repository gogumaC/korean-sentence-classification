# korean-sentence-classification

소프트웨어 융합기초4 : 한국어 문장 극성 분류 딥러닝 모델


---

## 1. 문제 제시

한글 문장을 입력으로 받아 문장의 ‘극성’을 분류하는 AI 분류모델을 만든다.

## 2. 데이터 구성 및 전처리

- 데이터 (데이터 출처 : [DACON - 문장 유형 분류 AI경진대회](https://dacon.io/competitions/official/236037/overview/description))
  |이름|설명|
  |---|---|
  |ID|샘플 id|
  |문장|전반적으로 한글 문장이며 특수문자, 숫자, 알파벳 등이 포함되어 있을 수 있다.|
  |유형|대화형/사실형/예측형/추론형|
  |극성|긍정/미정/부정|
  |시제|과거/미래/현재|
  |확실성|불확실/확실|
- 전처리
   1. 한글 데이터만 남기기 위해 정규식을 사용해 한글 및 띄어쓰기만 남김
   2. 형태소 분석 툴인 khaiii를 사용해 문장에 포함된 형태소 추출 (https://github.com/kakao/khaiii)
   3. 분리된 문장의 형태소들을 취합하여 어휘사전 생성.
   4. 어휘 사전(list)의 단어를 dictionary(단어:list인덱스)로 변경하여 각 단어를 특정 정수에 맵핑
   5. pad_sequence를 사용하여 길이 맞춤
   6. 단어 임베딩을 사용하여 실수 벡터로 단어표현
     
## 3. 문제 해결 방법

이 프로젝트의 데이터는 순서가 있는 텍스트이므로 RNN을 사용하여 모델을 생성하는 것이 적합해 보였다.
SimpleRNN , LSTM , GRU 등을 사용한 신경망으로 각각 모델을 생성하고 비교하여 더 뛰어난 성능을 가진 모델을 생성한다.

## 4. 학습 방법에 따른 결과

### SimpleRnn

![image](https://github.com/gogumaC/korean-sentence-classification/assets/59639035/5691fd61-6215-41e7-82bd-ffd12d84c4a0)

(좌 - loss , 우 - accuracy)
loss = 0.0999 , accuracy =0.9715

에포크 1~2부터 손실이 발산하며 정확도 또한 감소하고 있다. 그래프 상의 손실률 증가와 정확도 감소는 수치상 미미하지만 저런 형태로 학습을 더 진행하면 과대적합이 될 것이다. 

### LSTM

![image](https://github.com/gogumaC/korean-sentence-classification/assets/59639035/4c836c42-016a-4dfb-87ae-4c9e17207b48)

loss=0.0836 , accuracy=0.974

### LSTM + dropout(0.3)

![image](https://github.com/gogumaC/korean-sentence-classification/assets/59639035/a1a8b2fb-0601-4ce4-8d42-5590c9d77ab8)

loss = 0.0717 , accuracy 0.0977

### LSTM + LSTM

![image](https://github.com/gogumaC/korean-sentence-classification/assets/59639035/c4f8da55-1052-4301-a9e8-60976122c296)

loss = 0.0992 , accuracy = 0.972

### GRU

![image](https://github.com/gogumaC/korean-sentence-classification/assets/59639035/501b6aef-ccee-4aeb-939d-d7bda3a8b852)

loss = 0.090 , accuracy = 0.972


## 결론 

keras의 SimpleRNN , LSTM , GRU를 각각 이용하여 모델을 구성해보았고 손실의 수렴 측면에서 보았을 때 SimpleRNN < LSTM < GRU 순으로 손실 그래프의 발산 폭이 줄어드는 모습을 확인할 수 있었다. 
따라서 이번 프로젝트에서 가장 성능이 뛰어난 모델은 GRU를 사용한 모델이라고 결론내렸다.
그렇지만 이번에 만든 모델들은 전반적으로 학습 과정에서  조기종료되는 에포크가 빠르고 수렴 형태를 갖추지 못한 경우가 많았다. 이에 대한 원인으로는 초기 에포크의 낮은 손실률과 높은 정확도를 보았을 때 초기 에포크에 학습이 잘돼서 이와 같은 결과가 도출되었거나 혹은 전처리가 미흡했던 것 이라고 추측한다.
특히 전처리 부분에서 이번에는 시간 및 난이도 상 분리된 형태소를 종류 및 분리 오류를 신경 쓰지 않고 모두 사용하였는데 이런 부분을 고려해 반영하고, 생략했던 맞춤법, 띄어쓰기 검사 등을 병행한다면 이보다 더 좋은 모델을 생성할 수 있을 것이라고 생각한다.


      
