## Fast R-CNN `27 Sep 2015`


#### `Abstract`

이전 RCNN에서 train과 test의 속도를 올리는 데에 집중함. 물론 성능도 좋아졌다.    
Fast RCNN은 test time에서 RCNN 보다 213배 더 빠르고, SPPNet 보다는 3배 더 빠르다.  

---

<br>

#### `Introduction`

이미지 분류와 달리 객체 탐지는 문제 해결에 더 다양한 방법들이 필요하다.   
단순히 이미지 내에 어떤 물체의 유무를 찾는 것이 아니라 객체의 정확한 위치를 짚어줘야하기 때문.

<br>

복잡성은 Proposal 이라 불리는 후보 위치들을 찾아내는 것과 정확한 Localize를 위해 한 번 더 디테일하게 처리해주는 것에서 비롯된다.  
이런 복잡성 때문에 기존 RCNN과 같이 Multi-Stage로 진행되는 학습은 매우 오래걸리고 또 메모리 소비가 크다. 

본 논문에서는 `Convolutional Net 기반`의 SOTA 객체탐지 훈련 방식을 따른다. 하지만 이전과 달리 `One Stage` 방식이다.

<br>

- R-CNN

RCNN은 Convolutional Net 기반의 객체 탐지기로 성능이 좋았다. 그럼에도 불구하고 몇 가지 약점이 존재한다.

1. Training is Multi-state 

        후보지들을 찾고, 이 후보들을 정제하는 2 Stage 처리 방식


2. Training is expensive in space and time

        이미지에서 후보 위치들을 모두 골라 저장해놓은 상태에서 후처리를 하므로, 후보 객체 이미지만큼의 메모리 소모

3. Object detection is slow

        1장의 이미지에서 객체를 탐지하는 데 약 42초가 걸린다.
        
       
RCNN은 Convolution Net을 forward pass하며 각 객체의 Proposal을 찾고, 이 후보지들에 대한 정보를 공유하지 않고 처리 하기 때문에 매우 느리다.       

<br>

- SPPNet

Spatial Pyramid Pooling Networks (SPPNet) 은 RCNN보다 연산 속도에서의 향상을 이끌어냈다.  

이는 우선 전체 입력 이미지를 Convolutional을 통해 feature map을 뽑아낸다. 

이 feature들은 이후 각각 다른 크기를 가진 Maxpooling filter를 통해서 다른 크기의 feature map으로 뽑힌다.

이 Multi scale의 결과들은 Spatial Pyramid 형태로 Concatenate된다. 

이 방법을 통해 SPPNet은 RCNN 보다 10 ~ 100배 정도의 속도 향상을 이끌어냈다.  
그럼에도 여전히 약점을 가지고 있는데, 이 또한 multi-stage 훈련 방법을 사용한다는 것 등이 있다. 

<br>

- Fast RCNN

Fast RCNN은 4 가지 Contribution을 제공한다.

1. RCNN , SPPNet보다 우수한 mAP 성능 in dectection
2. Single-Stage train
3. training이 모든 Network layer를 훈련시킬 수 있다. (이전에는 Conv net 따로, SVM 따로 .. )
4. Feature를 Caching하는데 메모리를 소모하지 않는다.

<br>

---

#### `Architecture`

<div align='center'>
  
![image](https://user-images.githubusercontent.com/59076451/147423377-201a9c34-2054-4e56-a0bb-78f9106564c0.png)  
    
</div>  
  
RCNN은 약 2000장 정도의 Proposa을 각각 CNN 모델에 입력해서 독립으로 학습시킨다.   
따라서 매우 많은 시간이 소요된다.
  
하지만 Fast RCNN은 1장의 이미지만을 입력받으며, CNN 모델에 넣어주기 위해 Proposal을 Warp 시키지 않고,ROI Pooling을 통해 고정된 크기의 Feature Vector를 CNN 모델에 넘겨준다.  
  
  
<div align='center'>
  
![image](https://user-images.githubusercontent.com/59076451/147423201-34851cce-b463-457a-9f32-b6de7dc50854.png)
  
![image](https://user-images.githubusercontent.com/59076451/147423472-7aa11a3a-1d6f-483d-a9e9-a4cf2530fee6.png)
  
</div>

위 구조의 구체적인 동작 원리는 다음과 같다.

먼저 원본이미지를 CNN 모델에 넣어 Feature map을 얻는다.

    여기서는 800 x 800 -> 8 x 8 이미지로 축소시켰으므로 1/100 로 Pooling 시킴
    
위와 동시에 입력 이미지에 대해 Selective Search를 통해 Region Proposal들을 얻는다.

    이 Region Proposal들은 Pooling되지 않아 위 예에서와 같이 500 x 700의 크기를 가지고 있다.
    
다음으로 CNN을 통과시킨 Feature map에서 Region Proposal에 해당하는 영역을 추출한다. 이 과정이 바로 `ROI Projection`이다. 

입력 이미지는 1/100 크기로 줄어든 상태이므로, 이 줄어든 크기에 맞게 region proposal을 투영해주어야한다.

    Region Proposal의 Anchor Point, Width, Height와 1/100 비율을 이용해서 Feature map으로 투영시킨다.
    
    즉, 여기서 5x7 크기의 feature map 내의 ROI를 투영시킴.
    
최종적으로 2x2 크기의 Pooling 결과를 얻기 위해 알맞게 5x7 window를 sub grid로 잘라 Pooling 시킨다.

이렇게 얻은 ROI Feature map을 FC Layer에 통과시켜 마지막 2개의 출력 단으로 보내준다.

마지막 2개의 출력 단은 다음과 같다.

1. Softmax Layer (Classifier)

        배경을 포함한 K+1개의 출력

2. Bounding Box Regressor (Regressor)

        4개의 Bounding Box 좌표를 알려주는 (K+1) x 4개의 출력 

<br>

<div align='center'>
  
![image](https://user-images.githubusercontent.com/59076451/147423619-adf2cec9-d10f-4133-bbb9-4f8cd09d8121.png)
  
</div>  

RCNN과 달리 Multi task Loss를 사용하여 End to End로 학습을 진행한다.

    즉, SVM, Conv Net, Bounding Box Regressor에 대한 Loss를 위와 같이 정의하여 한 번에 학습한다.
    
또한 RCNN에는 학습 시 Region Proposal이 모두 달라 이를 학습할 시 연산을 공유할 수 없었다.

하지만 Fast RCNN에서는 SGD Mini Batch를 구성할 때, N개의 이미지를 가져와서, R개의 ROI를 사용한다고 할 때, 각 이미지로부터 R/N개의 Region Proposal을 샘플링하는 방법이다. (`Hierarchical Sampling`)

    이를 통해 같은 이미지에서 추출된 Region Proposal 끼리는 Forward , Backward 연산시 메모리를 공유할 수 있다.

---

<br>


#### `training`

<div align='center'>
  
![image](https://user-images.githubusercontent.com/59076451/147423201-34851cce-b463-457a-9f32-b6de7dc50854.png)
      
</div>

1. CNN 모델로는 VGG16을 사용한다. 

        마지막 Max Pool Layer는 ROI Pooling layer로 대체한다.
    
2. 원본 이미지에 대해 Selective Search를 적용하여 Proposal들을 추출한다.    

3. VGG 모델에 원본 이미지를 입력하고 Feature map을 추출한다. 

![image](https://user-images.githubusercontent.com/59076451/147423860-3c3b14bc-b85e-47ec-964e-54d2a49d334d.png)

4. 위 두 가지 Featurea map을 ROI Pooling 한다. 

![image](https://user-images.githubusercontent.com/59076451/147423891-e5651a53-db23-4193-8fcd-f9d9ea370c4b.png)

5. ROI Pooling 결과를 FC layer를 통과시켜 마지막 2개의 Sibling layer로 보내준다.

6. Loss 계산 후 Back Propagation 수행 - Update


#### `Test (Detection)`

<div align='center'>

![image](https://user-images.githubusercontent.com/59076451/147423972-a950fc65-1a8a-4651-af57-bc7212d0fb8e.png)

</div>

실제 Test에서는 훈련과정과 큰 차이는 없으나 마지막 단에서 Non Maximum Suppression을 사용한다.

이를 통해 최적의 Bounding Box만을 출력한다.

---

#### `Result`


<div align='center'>
  
![image](https://user-images.githubusercontent.com/59076451/147424013-af1f642d-7b86-40ed-9e6e-9361fed69be1.png)
  
</div>  

각각 VOC 2007, VOC 2010, VOC 2012 데이터셋에 대해서 성능을 비교한 결과이다.


---

#### `Conclusion`

Fast RCNN은 RCNN과 SPPNet에 비해 매우 간단하고 빠른 훈련과 업데이트 속도를 보였다. 

또한 SOTA 모델보다 좋은 Detection 성능을 보인다. 

<br>

- 참고 블로그 및 논문

https://herbwood.tistory.com/8

https://arxiv.org/pdf/1504.08083.pdf




