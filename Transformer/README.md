# Transformer "All you need is Attention" `6 Dep 2017`

## Abstract 

Recurrent한 모델을 사용하지 않고서 Seq2Se2 구조를 어느정도 따르며 Attention만으로 모델을 고안하였다.<br>
결과적으로 현재 state of the art 모델들 보다 성능이 우수하며 *병렬화가 가능하다*는 큰 장점을 가진다.
  - Recurret 한 모델들은 Sequential한 특징 때문에 병렬화가 불가능했다.
  - Sequence가 길어질수록 병렬화의 필요성은 더욱 커진다. 
    - Sequence가 길어짐에 따라 입출력 간 단어의 간 길이가 길어질수록 Dependency 문제가 발생하는데 이는 Attention 기법을 통해 해결할 수 있었다.
      - Attention : Decoder에서 출력 단어를 예측하는 매 Step마다 Encoder에서의 전체 입력 정보를 한 번 더 참고하는 방법<br> 다만 전체 입력 정보를 다 동일 비율로 참고하는 것이 아니라 해당 시점에서 특별히 더 주목해야할 부분을 더 집중해서 참고하게 된다. 

## Introduction 

Transformer는 Sequential한 데이터를 처리하는 기존 모델들의 병렬화 문제를 해결하기 위해 고안되었다.<br>
NLP에서 큰 도약이었던 Attention 기법 조차 모두 Recurrent한 모델들과 같이 사용되고 있었다.<br>
  - 기존 Recurrent한 모델들은 일반적으로 입출력의 Sequence의 특정 위치에 따라 계산을 분해하여 진행한다.
  - 즉, 재귀 모델은 이전 정보들을 모두 사용하여 학습을 진행하기 때문에 Training과정에서 병렬화가 불가능하다.
  - Sequence가 길어질수록 메모리 제약, 연산량 등에서 더욱 Critical한 문제가 된다.<br>최근 연구들이 이 성능을 많이 발전시켰지만 여전히 Sequence 연산에 대한 병렬화 문제는 남아있다.
  - Attention 기법은 입출력 간 Seuqence 길이에 따른 의존성 문제를 해결한다. 
  - Attention 기법은 모두 재귀 모델과 함께 사용된다.

#### 해당 논문에서는 이 재귀적 모델을 배제하고 입출력 간의 Global한 Dependency를 모델링 할 수 있게 Attention 기법만을 사용한 Transformer를 제안
  - Transformer는 병렬화를 가능하게 하며 실제 Tranining에 걸리는 시간도 훨씬 적다.

## Background

Sequential한 연산에 대한 성능을 높이는 연구는 계속 있어왔고, 이를 시도한 모델들은 모두 은닉 상태와 입출력 연산 모두에 CNN을 Building Block으로 사용했다.<br>
하지만 이 모델들에서는 입출력 간의 두 위치로부터 신호를 관계시키기 위해 필요한 계산 수가 그 위치 간 거리에 따라 크게 증가한다.<br>
이는 곧 Dependency를 학습하는데 문제를 야기하게 된다. 

반면에 Transformer에서는 상수 번의 계산 만이 필요하다. 비록 Attention Weight가 상대적으로 평활화되어 해상도(Resolution)가 감소되는 문제가 있지만, 이는 Multi-Head Attetion기법을 사용하여 상쇄할 수 있다. 
  - 이 부분은 뒤에 조금 더 자세하게 이야기한다.

"Self-Attention" 기법은 한 Sequence의 Representation을 연산하기 위해 각기 다른 위치에 있는 요소들을 관련 짓는 Attention 기법이다. 
  - 이후 Attention 기법에 대해 조금 더 자세하게 이야기할 것.


## Model Architecture

### 1 Encoder and Decoder Stacks

![111](https://user-images.githubusercontent.com/59076451/125040637-db2f5b80-e0d2-11eb-929a-abacfb661a7c.PNG)

우선 Transformer는 Seq2Seq의 Encoder-Decoder 구조를 사용한다.
Encoder는 입력 시퀀스의 연속적인 표현인 x1, x2, x3, ... , xn을 다른 연속적인 표현인 z1, z2, z3, ...., zn으로 Mapping한다. <br>
이 z 를 가지고 Decoder는 출력 시퀀스인 y1, y2, y3, ... , yn을 생성한다. <br>
각 Step에서 다음 Representation을 생성할 때, 모두 이전 정보들을 추가적인 입력으로 사용하기 때문에 Auto-Regressive하다.

Encoder와 Decoder를 각각 내부적으로 Self-Attention과 Position-Wise FC layer를 쌓아서 구성한다.

- Encoder
 
총 6개의 Encoder layer를 쌓아올려 구성한다. 각 layer들은 2개의 Sub-layer로 구성된다.<br>
첫 번째 Sub-layer는 Multi-Head Self-Attention layer이며 두 번째 Sub-layer는 Position-Wise FC feed-forward layer이다. 

또한 추가적으로 각 Sub-layer마다 Residual Connection을 차용하였고, 이무 정규화가 뒤따르도록 구성한다.

- Decoder

동일하게 6개의 Layer를 쌓아서 구성한다. <br>
각 Layer는 Encoder와 동일한 2개의 Sub-layer사이에 Encoder 출력에 대해 Multi-Head Self-Attention을 수행하는 Sub-layer를 추가배치한다. <br>
또한 은닉 상태 입력단에서 Masking 기법을 사용하는데, 이는 해당 Time step를 기준으로 미래에 해당하는 정보들의 접근을 막기 위해 사용된다. <br>
  - Masking : Decoder의 Auto-Regressive한 성질을 보존하기 위해 Leftward로의 정보흐름을 막아야한다. (해당 Step 기준으로 오른쪽 정보들의 유입)
  - 이는 해당 시점 기준 미래 정보들을 미리 조회함에 따라 현재 단어 결정에 영향을 미칠 수 있는 위치에 해당하는 값들에 -inf 값을 주어 Softmax 연산 결과 0에 가까운 값을 갖도록 하는 방식으로 Masking을 진행한다.





