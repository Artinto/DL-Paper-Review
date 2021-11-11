# Big Bird: Transformers for Longer Sequences `2020.7`  
NeurIPS 2020 | Google Research | 단순 성능향상을 보이는 논문이 아니라 이론적으로 접근하여 Transformer의 효율성을 높인 논문

## Abstract

#### 기존연구의 특성 및 한계점
- Transformer 기반 모델은 학습을 위해 엄청나게 많은 데이터셋이 필요하다. 
- Transformer의 self attention은 데이터 배열의 모든 조합을 다 계산하기 때문에 많은 메모리가 필요함.
- 이때 사용되는 full attention 메커니즘의 계산은 시퀀스 길이에 대한 2차 의존성을 가진다.  

**(한계)** 모델의 성능을 향상시키고, 데이터가 긴 문장일수록 더 큰 메모리와 좋은 GPU가 필요하다.

<br>

**(제안) BIGBIRD**는 기존 Attention의 **2차 의존성**을 **선형**으로 줄인 **sparse한 attention mechanism** 이다.

<br>


separse attention은 선형 메커니즘을 사용함에도 불구하고, 2차 full attention model의 속성을 그대로 가지고 있다.    
이는 아래의 두가지를 증명함으로써 보여준다.    
- sparse한 attention이 적용된 인코더는 시퀀스 함수의 universal approximator이다.
- sparse한 attention이 적용된 transformer는 Turing complete하다.
  >  Turing complete라는 것은 튜링 머신과 동일한 계산 능력을 지닌다는 의미라고 함.

<br>

**[장점]**
- sparse attention는 **global token**으로 전체 시퀀스에 대해 참고(attend)한다. 
- 이전에 하드웨어가 가능했던 것보다 최대 8배 길이의 시퀀스를 처리할 수 있다. 

<br>

**[결론]** BIGBIRD는 질문 답변 및 요약과 같은 긴 context를 처리하는 task에서 좋은 성능을 보인다.

<br>

## Introduction 
트랜스포머는 입력문장의 각 토큰이 시퀀스의 다른 모든 토큰에 독립적으로 attend하는 self attention 메커니즘을 도입하여     
LSTM과 같은 순환 신경망에서 사용했던 순차적 의존성(sequential dependency)에서 벗어나    
**병렬적**으로 attention을 진행할 수 있게 되었다. 


<br>


self attention과 Transformer가 좋은 것은 알겠으나
- 성능을 위해 self-attention 모델의 연산이 필수적인가? (효율적인가) 
- a fully quadratic self-attention의 성능을 더 적은 연산을 사용해도 얻을 수 있다면
이걸 만족하는 sparse attention 메커니즘은 기존의 self attention의 속성가지고 있을까?

위 질문에서 시작된 아이디어로 **Big Bird는 선형복잡도의 연산으로 self attention의 성능내는 attention 메커니즘**이다.


<br>

 **◼ BIGBIRD 구성**    
 BIGBIRD는 3가지 attention으로 구성되어있다.
 

![image](https://user-images.githubusercontent.com/43063980/140293008-42fb1972-4a03-4024-a55b-2b81f1938e43.png)
- random attention : query와 r개의 랜덤 key 간의 attention
- window attention : query 양옆 w개의 key와 attention
     > 언어가 인접한 단어에 가장 큰 영향을 받는 점을 모델링한 부분
- **global attention** : query와 g개의 global token과 attention. 멀리 떨어져있는 토큰들에 대해서 연관성을 반영해준다.
     > global token : 시퀀스의 모든 token에 attend하고 모든 token으로부터 attend받는 token set이며 기존 self attention의 성능을 끌어내는 가장 중요한 부분. 
         
     > global token에 따라서 Big bird-itc / Big bird-etc로 구분된다.    
     
     > internal transformer construction (ITC) : 기존의 token 중 일부를 global token으로 만든다.    
     > extended transformer construction (ETC) : CLS 같이 별도의 token을 도입하여 그 token들을 global token으로 만든다.  
       
     > Big bird-etc의 경우, 현재 HotpotQA 데이터셋에서 SOTA이다. 

![image](https://user-images.githubusercontent.com/43063980/141291827-a4a4b1eb-bf43-481d-b92c-06779a756805.png)


 
<br>

### ◼ Contribution
1. BIGBird 시간복잡도 O인 함수로 시퀀스 함수를 표현할 수 있다.
BIGBird는 Turing complete하는 성능을 보인다. 
2. 질문 답변 및 문서 요약 등의 NLP task에서 좋은 성능을 달성한다.


<br>

## BigBird Architecture
<p align="center"><img src="https://user-images.githubusercontent.com/43063980/140302361-91959632-4fa5-46bd-987d-b38ae67f7a29.png" width = "60%"></p>

<p align="center"><img src="https://user-images.githubusercontent.com/43063980/140302370-2a0b1f24-6bba-4e7a-919f-18b34252823a.png" width = "40%"></p>

> (위) BigBird Attention 수식 - 시간복잡도 O    
> (아래) 기존 Transformer Attention 수식 - 시간복잡도 O^2

<br>

<img src="https://user-images.githubusercontent.com/43063980/141293100-a968dd43-1367-4706-b204-7ff40db16cf6.png" width = "70%">
Ni는 node i 가 가리키는 node (ex. global token, 근거로, 비과세, 하나로, 한다)    

Xi는 토큰 임베딩 (ex. 세뱃돈은)

<img src="https://user-images.githubusercontent.com/43063980/141293836-61639975-c5f2-4dcb-b4e6-b586692a4f0c.png" width = "60%">

Qh, Kh : 쿼리, 키 함수

Vh : value 함수



### ◼ Experiments

[Base size Model results]
![image](https://user-images.githubusercontent.com/43063980/140308659-e91b865b-4682-4493-97a1-d8cdc4ef3483.png)

[Fine-tuning results]
QA tasks의 Test set으로 Fine-tuning한 BigBird모델의 결과
![image](https://user-images.githubusercontent.com/43063980/140308768-079c501a-53a5-45d7-906f-4f6f12bf40b0.png)

[spare attention을 사용한 encoder-decoder의 성능]
![image](https://user-images.githubusercontent.com/43063980/140309373-69844112-eeb0-4286-ae4f-567da154d282.png)



