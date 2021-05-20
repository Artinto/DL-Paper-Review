# GoogleNet - Going Deeper with Convolutions 'seo 16=7 2014'

*논문 순서와 별개로 이해하기 편한 흐름으로 정리하였음*

## Abstract

GoogleNet은 기존의 CNN 구조를 향상시키기 위해 고안된 새로운 모델이다.
단순히 CNN 성능을 높이는 방법은 다음과 같다. 
  
  1. depth를 늘리는 것
  2. 각 layer의 unit/cell을 늘리는 것

당연하게도 각 방법에 대한 부작용이 존재한다.

  For 1 method : Parameter수가 많아지기에 제한된 데이터에 대해서 overfitting의 위험도가 증가한다. <br>
  For 2 method : Computing power가 증가하게 된다. 
  
  ex) filter수가 늘어나게 되면 연산량은 그에 대해 Quadratic 하게 증가한다.
  
  ### GoogleNet은 여기에 따른 해결책으로 (depth를 늘리고, unit을 늘리며 연산량은 유지할 수 있는) Inception Module을 포함한 모델을 제시한다.
  
GoogleNet의 주요한 특징은 다음의 두 가지로 볼 수 있다.

  1. hebbian Principle
  2. Multi - scale Processing 

<br><br><br>

## Network in Network , 2013 (NIN) 

GoogleNet은 NIN 논문에서 시작한다.

NIN 논문에서는 Conv layer가 local receptive field에서 특징을 뽑아내는 능력은 좋지만, 정작 filter는 기껏 conv 에서 뽑아 놓은 비선형적 특징은 걸러내버리는 문제가 있다고 말한다.<br>이유는 filter가 linear하기 때문이다.

따라서 이 문제를 해결하기 위해 filter의 개수를 늘려서 비선형적인 특징을 더 많이 뽑아내고 걸러지고 나서 남는 feature가 많도록 해야 한다고 생각한다.
하지만 당연히 연산량의 문제로 마냥 filter를 늘릴 수는 없었기 떄문에 다른 방법을 고안한다.

  - NIN 논문에서는 해결책으로 Micro Neural Network를 제시한다. 
  ![fasdfasdfasd](https://user-images.githubusercontent.com/59076451/118940209-70dc2380-b98b-11eb-9ec7-11d05aa31ba2.PNG)
  
    - 다음 그림은 각 layer에서 Convolution을 filter가 아니라 MLP를 사용하여 각 Patch를 Swapping하며 feature를 추출하도록 한 것이다.
    

  
  






 









