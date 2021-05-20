# GoogleNet - Going Deeper with Convolutions 'seo 16=7 2014'

*논문 순서와 별개로 이해하기 편한 흐름으로 정리하였음

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

##

 









