# What Changes Can Large-scale Language Models Bring? Intensive Study on HyperCLOVA: Billions-scale Korean Generative Pretrained Transformers `2021.10` 

**대규모 언어모델**이 가져올 변화 : 수십억 규모의 한국형 자연어 생성 트렌스포머에 대한 연구


<br/>



## Abstract & Introduction
GPT3는 엄청나게 많은 데이터로 학습시킨 커다란 언어모델은 **zero shot, few shot**에서도 엄청난 성능을 갖는다는 것을 보여준 모델이었음. 

<br/>
  
<br/>
  

### GPT-3의 남겨진 이슈들
     
<br/>
  
<details>
<summary><strong> 비영어권에 경우, 성능이 낮고, 적용이 어렵다.</strong></summary>
    
  <br/>
  
  - train 코퍼스의 92.7%가 영어임.
  - 언어는 다른 특성을 가지고 있는데 이를 고려하여 다른 언어로 훈련시키는 방법, 적용시키는 방법을 모름.
  
  
<br/>
  
  1. HyperCLOVER는 561B의 한국어 토큰을 사전학습시켰으며, 82B의 규모를 가진 한국형 GPT3 모델이다.
  2. 한국어에 특화된 토큰화를 사용하여 학습력을 높였다.
      - 어떻게 561B의 토큰데이터를 모으고, 세분화했는지
      - 새로운 한글 토큰화 방법을 사용함.
          > 형태소분석기 + 바이트레벨 BPE 사용   (이때, 형태소분석기는 자체제작)
      
      - 다양한 한국어 downstream tasks 에 대해 좋은 성능을 보이며, zero-shot/few-shot 학습등에서도 좋은 성능을 보임.
    
   <br/>
  
</details>
 
<details>
<summary><strong> 다양한 사이즈를 가지고 있지 않다.</strong></summary>
    
  <br/>
  
  
  - GPT3는 엄청난 규모의 모델이 아닐 때, 성능이 떨어짐.
    
  <br/>
  
  
  1. 논문에서는 다양한 규모의 모델에 대해서도 구성하여 성능을 보이고 있다.
    
  <br/>
  
  
  
</details>
 
<details>
<summary><strong> prompt-based 학습기법에 대해서는 겅증되지 못했다.</strong></summary>
    
  <br/>
  
  
  - prompt-based 학습은 요즘에 사용되는 학습기법으로 빠른게 최적화를 시키는데, 이 기법이 대규모 언어모델에 적용되어 성능을 검증한 적이 없다.
      > prompt-based 학습이란 매개변수 업데이트 없이 모델의 성능을 개선하는 학습방법이라고 함.
    
  <br/>
  
  1. HyperCLOVER는 prompt-based 학습방식과 few-shot 학습에서도 좋은 성능을 보이는 것을 확인함.
    
  <br/>
  
  
</details>
  
<br/>
  
  
**HyperClOVER는 이런 점을 보안해서 나온 한국형 GPT3임.**


  
<br/>
  
    
<br/>
  
  
  

