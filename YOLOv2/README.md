# YOLO9000:Better,Faster,Stronger / 25Dec2016

## Abstract
Better : Accruacy , mAP 측면의 개선사항
        
Faster : 속도 개선

Stronger : 더 많은, 다양한 클래스 예측  

## Better

### 기존 YOLO 모델의 단점
  - Localization error
  
  - low recall

![image](https://user-images.githubusercontent.com/92671224/147917818-de017819-a2b5-4763-aa9e-61b2c13ff2f3.png)

### 1.Batch Normalization 적용

- 기존 모델에서 사용하던 Dropout Layer를 제거하고 Batch Normalization을 추가  
- 이를 통해 mAP 2% 이상 상승되는 것을 확인




### 2. High Resolution Classifier

- 대부분의 detection SOTA 에서는 ImageNet 데이터에서 학습시킨 pre-trained classifier를 사용  
  대부분의 classifier 입력이 256x256 보다 작음  
- 기존의 YOLO는 244x244 로 학습된 VGG 모델을 가져온 다음, 448x448 크기의 이미지를 학습
- 이를 학습 전에 Image Classification 모델을 큰 해상도 이미지에 대해서 fine-tuning 함으로써 해결  
- 약 4% mAP 증가


 

### 3. Convolutional With Anchor Boxes
![image](https://user-images.githubusercontent.com/92671224/147916618-d000dc97-b559-459e-a46f-3c96097a81e1.png)  

- 기존 yolo에서 Fully Connected Layer를 떼어내고 Fully Convolutional 으로 바꿈 
- 앵커 박스의 개념을 도입  
    사전에 크기와 비율이 모두 결정되어 있는 박스를 전제  
    앵커 박스는 적당히 직관적인 크기의 박스로 결정하고, 비율을 1:2, 1:1, 2:1로 설정하는 것이 일반적  
    YOLOv2에서는 학습을 통해서 이 박스의 위치나 크기를 세부 조정  
    처음부터 중심점의 좌표와 너비, 높이를 결정하는 방식보다 훨씬 안정적으로 학습이 가능  
    
- Anchor box를 사용함으로서 기존 이미지당 98개의 box를 예측할 수 있었다면, 적용 후에는 1000개 이상의 box 예측 가능

 

### 4. Dimension Cluster
![image](https://user-images.githubusercontent.com/92671224/147921153-cfded333-7967-4fcb-81ea-93d7998c24da.png)

- coco 데이터 셋의 바운딩 박스에 K-means clustering을 적용
- 가장 적합한 anchor box의 후보군을 찾음

![image](https://user-images.githubusercontent.com/92671224/147921322-2ac26718-38da-4a31-9d22-1843dd906a94.png)

- 더 높은 k를 설정할 수록 성능은 높아짐, but 성능 저하
- YOLOv2에서는 k = 5 로 정함

 

### 5.Direct Location Prediction

![image](https://user-images.githubusercontent.com/92671224/147922074-db0799b7-936a-4aa8-a089-3ec2201b37a7.png)

- anchor box를 정할 때 기존의 Predicted box의 중심좌표들이 초기 단계에 잘못 설정된다면 학습이 잘 안될 수 있음  


- 기존의 yolo가 그리드의 중심점을 예측
- yolov2에서는 left top 꼭지점으로부터 얼만큼 이동하는 지를 예측
- 이렇게 사용했을 때, 네트워크가 조금 더 안정적으로 변함
- mAP 5% 정도 상승

 

### 6. Fine-Grained Features

![image](https://user-images.githubusercontent.com/92671224/147922263-569458cb-72cd-4fa7-83eb-6bd302a8cf84.png)

- 기존의 yolo에서는 CNN을 통과한 마지막 레이어의 피쳐맵만 사용하여 작은 물체에 대한 정보가 사라진다는 비판이 있었음 
- yolo v2에서는 passthrough layer 도입 
- 높은 해상도를 가진 26x26x256 피쳐맵을 13x13x2048 크기로 리스케일하여 낮은 해상도의 피쳐맵과 합침 
- 13x13x3072 크기의 피쳐맵

 

### 7. Multi-Scale Training

- 기본 사이즈는 416x416 사용
- 작은 물체들을 잘 잡아내기 위해서 여러 해상도의 이미지를 학습시킴  
- 10번의 배치시마다 입력 이미지의 해상도를 바꿔주며 학습을 진행 

 
### Result

<img src="https://user-images.githubusercontent.com/92671224/147922727-fef37fc9-936d-4298-8291-a2a88309cfa4.png" width="50%" height="50%"/>





## Faster

### Darknet (GoogleNet 기반)
<img src="https://user-images.githubusercontent.com/92671224/147923134-2b5b6c07-5331-4d43-a19a-85667aae912b.png" width="50%" height="50%"/>


 - 일반적인 detection model에서 VGGNet을 많이 사용
 - Maxpooling을 줄이고 convolution 연산으로 대체
 - accuracy는 88%로 VGG-16의 90% 성능과 크게 차이나지않는 반면, 30십억의 계산량을 8십억으로 줄임


## Stronger   
yolo v2 아키텍쳐를 기반으로 총 9000개의 클래스를 어떻게 학습시켰는 지를 설명  


### 1. Hierarchical Classification

<img src="https://user-images.githubusercontent.com/92671224/147923554-36e6295f-c60c-426b-9e28-be6bd16f5c8d.png" width="50%" height="50%"/>

- 9000개 이상의 다량의 class에 대해서 classification을 수행할 경우 계층적으로 분류 작업을 수행  
- 이미지 넷 데이터를 보면 개과 안에 웰시코기니, 요크 셔테리어와 같은 라벨들이 속함  
- Softmax 연산을 수행할 때 전체 클래스에 대해서 한번에 수행하지 않고, 각 대분류 별로 Softmax를 수행함  

 

### 2. Dataset combination with WordTree

<img src="https://user-images.githubusercontent.com/92671224/147923627-f7db0c00-fe01-43fe-a531-bc86ea1f96f4.png" width="50%" height="50%"/>

- 계층 구조를 사용하여 저자는 coco와 imagenet 데이터 셋의 라벨을 합쳐 WordTree를 만듦


### 3. Joint classification and detection

- 데이터 셋을 합쳐(ImageNet Classification + COCO) 총 9418개의 클래스를 가진 데이터 셋을 만듦 
- 그러나 이 중 9000개의 클래스는 ImageNet Classification 데이터 셋에 속하였고, 이들은 Classification 라벨만 붙어있는 상태입니다.
- COCO 데이터셋이 현격히 적기 때문에 더 많이 샘플링 되도록 학습하는 이미지의 비율을 4:1로 맞춤


 
### Result
- ImageNet Detection Challenge 데이터 셋을 활용하여 성능 평가 진행
- 19.7 mAP, 낮지만 다른 DPM보다 좋은 성능
- detection 라벨이 붙은 데이터를 학습하지 못한 클래스에 대해서는 16.0 mAP

![image](https://user-images.githubusercontent.com/92671224/147925958-b5e2ccad-af49-4097-ba3e-f3826701e1dd.png)

- 동물들에 대해서는 성능이 괜찮은 것에 반해 물체에 대해서는 성능이 현격히 떨어짐

