# RNN, LSTM, GRU 개념 정리 및 비교


기존의 인공 신경망은 순서나 맥락이 고려되지 않은 단순한 구조였다.  
따라서 번역이나 주가예측과 같은 task를 해결할 수 없었다. 

연속성을 갖는 데이터를 학습하기 위해서 hidden layer를 도입하게 되었고,  
이를 통해 과거의 정보가 미래의 결과에 영향을 줄 수 있는 순환구조를 갖는 **순환신경망(RNN)** 이 나타났다.

하지만 초기의 순환신경망은 학습을 거치며 이전의 정보를 쉽게 잃어버렸고,  
앞단의 정보를 덜 잃어버리도록, 더 계산시간이 짧도록 발전한 것이 **LSTM**과 **GRU**이다. 

<br>

## RNN


<br>


## LSTM
RNN은 순환구조를 통해 데이터의 연속성을 학습할 수 있게 만들었지만  
데이터를 학습시키는 backpropagation 과정에서 **vanishing gradient 문제**가 발생했다.

RNN의 장기 의존성 (Long-Term Dependency) 문제를 **Cell state**를 추가하여 해결한 모델이 **LSTM**이다.

![image](https://user-images.githubusercontent.com/43063980/123243333-e18fd600-d51d-11eb-9904-cd90450aad9e.png)

- LSTM은 RNN과 마찬가지로 순환구조를 가지고 있다. 다만 순환하는 **모듈의 구조가 다르다**.  

<br>



### cell state 

<img src = "https://user-images.githubusercontent.com/43063980/123247802-36355000-d522-11eb-88d2-f9203b6c69f3.png" width="40%">

- 정보가 저장된 메모리로 학습을 거치며 update된다.
- 어떤 정보를 update시킬지는 **Gate**를 통해 결정된다.


<br>


### Gate

![image](https://user-images.githubusercontent.com/43063980/123250826-7b0eb600-d525-11eb-8114-dee99dad6b7d.png)

- 각각은 **forget gate**, **input gate**, **output gate**이다.

    - forget gate : 과거정보를 얼마나 잊을 것인지
    - input gate  : 현재정보를 얼마나 기억할 것인지
    - output gate : 다음 state로 보낼 output 결정
 
 - 이전 cell state는 3개의 gate를 거처 다음 cell state로 넘어간다 
 - 모든 gate는 sigmoid함수를 사용하여 cell state에 얼만큼 영향을 줄지 결정한다. 
       - 
    
- 과거의 정보를 잊어버리게도 만들고, 현재의 정보를 기억하도록 update시키는데만드는데 이 Control을 **Gate**라는 
- 이전셀에 사용되어진 정보를 합과 곱연산을 통해 정보를 저장하는 역할


## GRU




![LSTM](https://user-images.githubusercontent.com/43063980/123234314-cde07180-d515-11eb-9143-55437438fc24.png)
![GRU](https://user-images.githubusercontent.com/43063980/123234349-d5a01600-d515-11eb-8071-6aceac6b2ec4.png)
