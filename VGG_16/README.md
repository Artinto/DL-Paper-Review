# Very Deep Convolutional Networks For Large-Scale Image Recognition '10 Apr 2015'

## Abstract

  - Large_scale 이미지에 대해 Convolutional Network의 depth의 영향에 대해 논한다.
  
      - 해당 논문의 포인트는 복잡하고 어려운 기법들을 사용하지 않고서 단순히 16 ~ 19개의 layer를 알맞게 배치하였고, <br>이 결과로 다른 최신 모델들보다 더 뛰어난 성과를 거두었다는 것이다.
      
      - 비슷한 구조의 모델에서 depth만을 바꾸어가며 성능을 비교하며,<br>추가적으로 완전히 같은 모델에 대해 Batch Normn / LRN 등의 기법을 추가하고 비교하며 해당 기법의 효과 또한 잘 설명하고 있다.
      
      - 1x1 filter , 3x3 filter, 5x5 filter , 7x7 filter 즁 *3x3 filter를 사용*하며, <br>이를 제외한 filter를 사용하지 않은 이유에 대해서도 설명하고 있다.
    
    
***

### Model Architecture Experiments
  
![asda](https://user-images.githubusercontent.com/59076451/118099344-0ddd1080-b410-11eb-85b6-4bebd938c3bd.PNG)
  
 
  
