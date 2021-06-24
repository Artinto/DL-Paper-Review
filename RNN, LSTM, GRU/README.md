# RNN, LSTM, GRU 개념 정리 및 비교


기존의 인공 신경망은 순서나 맥락이 고려되지 않은 단순한 구조였다.  
따라서 번역이나 주가예측과 같은 task를 해결할 수 없었다. 

연속성을 갖는 데이터를 학습하기 위해서 hidden layer를 도입하게 되었고,  
이를 통해 과거의 정보가 미래의 결과에 영향을 줄 수 있는 순환구조를 갖는 **순환신경망(RNN)** 이 나타났다.

하지만 초기의 순환신경망은 학습을 거치며 이전의 정보를 쉽게 잃어버렸고,  
앞단의 정보를 덜 잃어버리도록, 더 계산시간이 짧도록 발전한 것이 **LSTM**과 **GRU**이다. 

<br>

## 1. RNN


<br>


## 2. LSTM(Long Short Term Memory)
RNN은 순환구조를 통해 데이터의 연속성을 학습할 수 있게 만들었지만  
데이터를 학습시키는 backpropagation 과정에서 **vanishing gradient 문제**가 발생했다.

RNN의 장기 의존성 (Long-Term Dependency) 문제를 **Cell state**를 추가하여 해결한 모델이 **LSTM**이다.

![image](https://user-images.githubusercontent.com/43063980/123243333-e18fd600-d51d-11eb-9904-cd90450aad9e.png)

- LSTM은 RNN과 마찬가지로 순환구조를 가지고 있다. 다만 순환하는 **모듈의 구조가 다르다**.  

<br>



## cell state 


<img src = "https://user-images.githubusercontent.com/43063980/123247802-36355000-d522-11eb-88d2-f9203b6c69f3.png" width="40%">

- 정보가 저장된 메모리로 학습을 거치며 update된다.
- LSTM은 과거의 정보를 잊어버리게도 만들고, 현재의 정보를 기억하도록하는데 어떤 정보를 update시킬지는 **Gate**를 통해 결정된다.


<br>


## Gate

![image](https://user-images.githubusercontent.com/43063980/123250826-7b0eb600-d525-11eb-8114-dee99dad6b7d.png)

- 각각은 **forget gate**, **input gate**, **output gate**이다.

    - forget gate : 과거정보를 얼마나 잊을 것인지
    - input gate  : 현재정보를 얼마나 기억할 것인지
    - output gate : 다음 state로 보낼 output 결정
 
 - 이전 cell state는 3개의 gate를 거처 다음 cell state로 넘어간다.
 - 모든 gate는 sigmoid함수를 사용하여 cell state에 얼만큼 영향을 줄지 결정한다.
 
       0.0X : 정보기억↑↑  
       0.9X : 정보기억↓↓
 

<br>


<br>


<br>

> **forget gate**
<img src = "https://user-images.githubusercontent.com/43063980/123253320-536d1d00-d528-11eb-8879-b73b36636c45.png" width="30%">

> **input gate**
<img src = "https://user-images.githubusercontent.com/43063980/123253709-b9f23b00-d528-11eb-80b9-52db0f3cdc01.png" width="30%">

- 각 gate의 식은 위와 같고 이들은 아래의 수식을 통해 cell state를 update한다. 

<br>

<br>

> **update** 

<img src = "https://user-images.githubusercontent.com/43063980/123258539-71d61700-d52e-11eb-910f-472f192283f7.png" width="40%">

<img src = "https://user-images.githubusercontent.com/43063980/123258513-68e54580-d52e-11eb-8767-7fd9a2823f6a.png" width="30%">



- input gate의 ~Ct를 보면 원래 RNN의 식과 동일하다. 
- 이전의 정보(Ct-1)와 forget gate를 연산하고 이번 cell에 대한 값(~Ct)은 input gate와 계산한다.   
- 과거정보와 현재정보가 합쳐진 cell state는 **다음 state로 넘어간다.**  


<br>

> **output gate**
<img src = "https://user-images.githubusercontent.com/43063980/123253790-d3938280-d528-11eb-8881-74813b49656f.png" width="30%">


- 최종적으로 얻어진 cell state 값을 얼마나 hidden state로 넘겨줄지 결정하는 역할
- cell state는 output gate를 거쳐 hidden state로 넘어간다.  

<br>

-> **RNN의 문제였던 장기의존성 문제를 cell state라는 레이어를 통해 해결했지만 다른 RNN계열보다 연산속도가 느리다는 단점이 있다.**

<br>

<br>



## 2. GRU(Gated Recurrnet Unit)
GRU도 마찬가지로 순환구조를 가지고 있다. 이 역시 모듈의 구조가 다르다.  
GRU는 더 간단한 구조로 이루어져 있어서 계산이 효율적이다. **(연산속도를 높였다.)**


<br>

**[LSTM과 비교]**
- LSTM에 비해 학습속도가 빠르다.
- 데이터가 적을 때, 좋은 성능을 보인다. (데이터가 많은 때는 LSTM의 성능이 더 좋다.)
- reset gate, update gate 총 2개의 gate가 사용된다.

<br>


## Gate
LSTM의 Input Gate와 Forget Gate가 GRU에서는 하나의 Update Gate로 합쳐졌다.  
LSTM의 cell state와 hidden state가 GRU에서는 하나의 hidden state로 합쳐졌다.


<img src = "https://user-images.githubusercontent.com/43063980/123234349-d5a01600-d515-11eb-8071-6aceac6b2ec4.png" width="50%">

<br>


> reset gate
<img src = "https://user-images.githubusercontent.com/43063980/123263212-b0ba9b80-d533-11eb-8799-84b6af4ea60d.png" width="30%">

- 이전의 hidden state를 얼마나 활용할지

> update gate


<img src = "https://user-images.githubusercontent.com/43063980/123276530-36dcdf00-d540-11eb-9b04-39a0c246ac92.png" width="30%">

- Zt(controller)가 동시에 forget과 input gate를 모두 제어한다

    > Zt : 현재정보를 얼마나 사용할지 (input gate)  
(1-Zt) : 과거정보를 얼마나 사용할지 (forget gate)



<br>


<br>


> update

<img src = "https://user-images.githubusercontent.com/43063980/123278831-3f361980-d542-11eb-8881-05f554cd58d3.png" width="30%">

- 현재에 대한 hidden state를 구하는 식
- 이전 hidden state을 얼마나 사용할지 Reset gate를 적용하여 현재 hidden state로 사용할 값을 구한다.



<img src = "https://user-images.githubusercontent.com/43063980/123278567-01d18c00-d542-11eb-9155-6d038360b208.png" width="30%">

- 과거의 hidden state와 현재의 hidden state를 각각의 비율(Zt, (1-Zt))에 따라 반영하는 식
- update gate를 거쳐 다음으로 hidden state 값을 넘겨준다.



<br>

-> **연산속도가 느리다는 LSTM의 단점를 모듈구조를 간결하게 만듦으로서 계산을 효율적이게 만들었다.**

