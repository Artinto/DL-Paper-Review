# You Only Look Once: Unified, Real-Time Object Detection `2016.5`


<details>
<summary><strong>Object Detection</strong></summary>
    
  <br/>
  
  ## Object detection = Localization + Classification
  - **Localization** (Reginonal proposal) : **물체가 있을만한 영역을 찾음**
  
  - (muti-labeled) **classification** : **어떤 물체인지 분류함**
  
<br/>
 
  <img src = "https://user-images.githubusercontent.com/43063980/146750745-bca837b3-71c6-4f72-8716-5ed535c8d361.png" width="60%">
  
  **( 1-stage detector와 2-stage detector로 나뉘어 발전 )**
  
  <br>
  
  <img src = "https://user-images.githubusercontent.com/43063980/146752911-d7dff51c-7085-4451-bbca-44b1ba53275b.png" width="80%">


  - regional proposal과 classification이 동시에 이루어지면, 1-stage detector 
  - regional proposal -> classification이 순차적으로 이루어지면, 2-stage detector
  
  <br>
  
  **YOLO는 1-Stage detector 모델로, 논문이 발표될 시기 R-CNN 등의 2-stage detector들이 대부분 연구되고 있었습니다.  
     2015년 YOLO가 발표된 뒤, 최근까지 발표된 논문들에도 기초가 되는 논문입니다.**
  > YOLOv2, YOLO9000, YOLOv3, YOLOv4, YOLOv5, PP-YOLO, YOLOR, YOLOX ...
  
  
</details>






## Abstract
- (2-stage detector 였던 RCNN과는 다르게) 단일신경망으로 bounding box와 class의 확률을 예측한다.
- 파이프라인 네트워크없이 end-to-end학습이 가능하다.  

**: 1-stage detector이다.**

<br>

- 실시간으로 처리할 수 있을만큼 아주 빠르다.
- 배경에서 잘못 detect하는 background error가 낮다.
- 일반적인 representation을 학습하기 때분에 다른 도메인에서도 잘 적용된다.


## 1. Introduction

- 기존 연구인 DPM 설명

   > DPM은 sliding window 기법을 이용  
   > 전체 이미지에 대하여 작은 구역에 일정한 간격(sliding window)을 두고 classifier를 실행
   
- 기존 연구인 RCNN 설명
  
  > R-CNN은 selective search를 이용하여 객체가 있을 법한 구역을 제안하고, 그 구역을 classifier로 분류 
  > 출력값으로 생성된 바운딩박스를 전처리과정(bounding box regression)을 거쳐 최종 바운딩박스를 선택
  
  - RCNN은 복잡한 파이프라인은 사용하기 때문에 느리고, 최적화하기가 어렵다는 단점 언급
    
<br>    

- **YOLO 설명 및 장점**

    - single convolutional network로 이미지를 입력받아, 여러 개의 바운딩 박스와 각 박스의 class를 예측한다.

1. 매우 빠르다
 
   - 실시간 물체인식이 가능하다.
   - (기존 2-stage detector는 느렸음)

2. 이미지 전체에 대해서 bounding box를 예측한다.
 
   - (Fast-RCNN은 [selective search](https://github.com/Artinto/DL-Paper-Review/tree/main/Selective%20Search)가 제안한 영역에서 bounding box를 예측)

3. 일반화된 representations를 학습한다. 
   - 다른 도메인에서 fine-tunning하여 적용했을 때, 좋은 성능을 보인다.

<br>


- YOLO는 기존 detection SOTA와 비교했을 때, Localization의 성능은 낮지만 속도가 훨신 빠르며, background error가 낮다.
- (1-stage와 2-stage는 속도 <-> 정확도가 trade off되는 관계를 보인다.)


<br>


## 2. Unified Detection
- end-to-end 방식으로 하나의 convolution network를 거쳐서 마지막 feature_map에서 bounding box와 class를 예측한다.
- 모델의  최종 feature map은 7 x 7 x 30의 사이즈이며, 하나의 feature map을 49개의 grid cell로 보고 각 grid cell에서 2개의 bounding box와 class를 예측을 하게 된다.

- 7 x 7 x 30 == S x S x (5 x B + C)  
  > S = feature size : 7  
5 = (cx, cy, w, h, confidence)  
B = number of boxes : 2  
C = classes : 20 (PASCAL VOC dataset)  

### 2-1. 네트워크 구조
![image](https://user-images.githubusercontent.com/43063980/146762642-d28325b9-c61e-4bb4-a890-9d4078612f1d.png)
- GoogLeNet의 네트워크 구조를 모티브로 하였고 총 24개의 conv layer와 2개의 FC layer를 포함한다.

<br>

### 2-2.Training
 Multi Loss 사용
 
 
### 2.4  YOLO의 한계
- YOLO는 1개의 grid cell당 1개의 class만 취급하기 때문에 2개 이상의 물체들의 중심이 한 grid cell에 모여있더라도 한가지의 class만 예측할 수 있다. 
  > 작은 물체들이 모여있을때 감지하지 못한다.
  
- 일정한 비율의 bbox로 예측을 하다보니 스케일이 다른 물체에 대한 예측이 좋지 못하다. 
- 작은 bbox의 loss와 큰 bbox의 loss를 동일하게 처리한다. 


## Experiments
### PASCAL VOC data 에서의 성능비교
![image](https://user-images.githubusercontent.com/43063980/146763328-e693b8b2-b784-4f1f-bb2d-72acec1f4b51.png)

### Fast RCNN과 비교
![image](https://user-images.githubusercontent.com/43063980/146763428-5cb063c5-330d-49a4-8040-6a6d3c8729e0.png)

### 다른 도메인에서 성능비교 
![image](https://user-images.githubusercontent.com/43063980/146763534-9cd35ed7-f4f2-419e-9648-5d3304c12e47.png)

confidence score , mulit loss
YOLO network 동작방식(출력위주로)
