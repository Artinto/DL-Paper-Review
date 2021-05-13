# Very Deep Convolutional Networks For Large-Scale Image Recognition '10 Apr 2015'

## Abstract

  - Large_scale 이미지에 대해 Convolutional Network의 depth의 영향에 대해 논한다.
  
      - 해당 논문의 포인트는 복잡하고 어려운 기법들을 사용하지 않고서 단순히 16 ~ 19개의 layer를 알맞게 배치하였고, <br>이 결과로 다른 최신 모델들보다 더 뛰어난 성과를 거두었다는 것이다.
      
      - 비슷한 구조의 모델에서 depth만을 바꾸어가며 성능을 비교하며,<br>추가적으로 완전히 같은 모델에 대해 Batch Normn / LRN 등의 기법을 추가하고 비교하며 해당 기법의 효과 또한 잘 설명하고 있다.
      
      - 1x1 filter , 3x3 filter, 5x5 filter , 7x7 filter 즁 *3x3 filter를 사용*하며, <br>이를 제외한 filter를 사용하지 않은 이유에 대해서도 설명하고 있다.
    
    
### 결론
  
  - 기존의 단순한 ConvNet에 복잡한 기법을 추가하거나 depth를 무식하게 늘리지 않아도, <br> 적당한 깊이와 알맞은 filter, 구조만 갖추면 최신 기술 모델 보다 더 뛰어난 성능을 낼 수 있다.
    
***

### Model Architecture Experiments
  
  ![asda](https://user-images.githubusercontent.com/59076451/118099344-0ddd1080-b410-11eb-85b6-4bebd938c3bd.PNG)
  
 
  
## ConvNet Architecture Setting 

1. Input Image
    
    224x224 RGB 3 Channel 이미지 -> 0 ~ 1사이로 정규화 <br> 그 외 데이터 전처리 x

2. Padding

    Padding = 1 -> Spartial Resolution(공간 정보)을 그대로 보존시키기 위함 
    
    - Train과 다르게 Test에서는 뒷 단의 FC layer -> Conv layer로 바꾸는 FCN 을 사용하는데, <br> 이를 위해 Spartial 정보는 최대한 유지하려함
     - FCN Model을 사용하는 이유 : 물체의 공간 상의 정보에 대해 모델이 Robust하게 하기 위함이며, <br> Input Image의 크기에 제한적이지 않아 모델 구조가 더 유연해질 수 있기 떄문

    - Train으로 Conv Net 앞 단의 Weight를 학습하고, 이를 그대로 FCN으로 Transfer Learning을 진행 
    
3. Pooling

    2x2 Max Pooling with Stride 2
  
  - 연산량을 줄이고 Key feature만을 걸러내기 위한 layer이며, Conv layer에서 spartial 정보를 유지하고 있기 때문에 <br> Max pooling 결과로 얻어지는 결과 또한 공간 정보를 더 많이 가지고 있을 가능성이 높다.

4. Filter

  - 논문에서 가장 자주 거론한 Architecture 구성 요소이며, 3x3 filter를 사용한다.
  
    1x1 filter, 5x5 filter , 7x7 filter 를 사용하지 않은 이유는 정리하자면 다음과 같다.
    
    - 우선 5x5, 7x7 filter 를 1번 사용한 것과 3x3 filter를 3번 사용한 결과는 동일하다. <br> 즉 , Output Size가 동일하다. <br> 하지만 filter를 3개를 쓰는 것이 1개를 쓰는 것을 택한 이유는 다음과 같다.
     
      - Non-Linearity를 더 많이 반영할 수 있다.
      - 연산에 필요한 Parameter 수가 더 적다.
        
        ( 3x(3x3xCxC) vs. 7x7xCxC for C channels per layer (3x3xC+3x3xC+3x3xC < 7x7xC) )
      

5. FC Layer


6. Activation Func

7. Addional Skill 



  
