# ImageNet Classification with Deep Convolutional Neural Networks

## Abstract

- 1000개의 클래스의 1.2 million high-resilution image를 분류
- 5개의 convolutional layers와 maxpooling layers, 3개의 fully connected layer 그리고 다클래스 분류를 위한 softmax function
- AlexNet의 차별화된 기법으로 dropout, GPU 사용
### 결과
- ILSVRC-2012에서 top-5 test error rate이 2위와 10.9%차이를 보였다.
<br><br>

## Model

### 데이터 전처리
- 이미지들을 동일한 크기(256x256)으로 resize

### Architecture
#### ReLU
- activation function을 기존 CNN에서 쓰이던 f(x) = tanh(x) 또는 sigmoid가 아닌 f(x) = max(0,x)를 사용
- 기존 함수들은 vanishing gradient를 유발 할 수 있고 large datasets에서 ReLU와 다른 함수와의 속도 차이가 크다.  
![KakaoTalk_20210514_130212144](https://user-images.githubusercontent.com/77203609/118219595-c3fa3600-b4b4-11eb-812d-d41ca4cf925b.png)
-  ReLUs (solid line), tanh neurons(dashed line) CIFAR-10에 대하여 6배 더 빠른 ReLU 

#### Training on Multiple GPUs
- 이 논문에서는 GPU를 두 개로 나누어서 사용
- 결과적으로 CNN연산들을 GPU로 계산함을써 학습속도도 더 빨라지고 error rate 또한 줄어든다

#### Local Response Normalization(LRN)
- lateral inhibition (측면 억제) 개념 사용
- ReLU는 vanishing gradient를 방지하지만 주어진 양수값을 사용하기 때문에 conv나 pooling시 매우 높은 하나의 픽셀값이 
  주변의 픽셀에 영향을 끼치게 되므로 이를 방지하기 위해 Activation map의 같은 위치에 위치한 픽셀끼리 정규화를 한다.
  
#### Overlapping Pooling
- stride = s, pooling을 통해 얻어지는 feature size를 zxz라 하면 s < z 일떄 오버풀링을 하게된다.
- 논문에서는 Overlapping Pooling으로  top-1, top-5의 error rate을 0.4%, 0.3%까지 줄였다.

### Reducing Overfitting
####  Data Augmentation
- Overfitting을 줄이는 방법중 하나는 데이터를 늘리는 것인데 논문에서는 2가지 방법사용 : horizontal reflections, PCA on RGB pixel values
1. horizontal reflections
  - 256x256으로 resized된 이미지들을 224x224의 size로 random crop한다.
2. PCA on RGB pixel values
  - RGB값을 갖는 pixel값 들의 3x3 공분산 행렬을 통해 PCA분석을 하면 eigenvector와 eigenvalue를 얻을 수 있다.
  ![KakaoTalk_20210514_135038924](https://user-images.githubusercontent.com/77203609/118222919-7634fc00-b4bb-11eb-8738-d16dc22112a8.png)
  - 위와 같은 식으로 원본이미지 변경
  
  ![KakaoTalk_20210514_135250501](https://user-images.githubusercontent.com/77203609/118223205-e2affb00-b4bb-11eb-8604-2164e63475d5.png)
  - PCA 적용 예시
3. Dropout
  
![dropout](https://user-images.githubusercontent.com/69898343/118241994-c6ba5280-b4d7-11eb-97c4-d330f8d84a05.png)

- 다음과 같이, overfitting을 방지하기 위하여 랜덤한 뉴런을 배제한 후 학습을 통하여 결과를 도출 하는 효과인 Dropout의 개념을 도입하였다. 

### Overall Architecture

![alxnet](https://user-images.githubusercontent.com/69898343/118252613-ea839580-b4e3-11eb-800c-7a41214f7a65.png)

1. Convolution 1 Layer

- 227 X 227 X 3 의 이미지를 각 각 48개의 11 X 11 X 3의 필터로 Convolution (stride =4, padding =0) = 2개의 (55 X 55 X 48)

- 55 X 55 X 48 의 이미지를 3 X 3 (stride =2) 으로 Max Pooling = 2개의 (27 X 27 X 48)

2. Convolution 2 Layer

- 27 X 27 X 48의 이미지를 각 각 128개의 5 X 5 X 48 필터로 Convolution (stride =1, Padding =2) = 2 X (27 X 27 X 128)

- 27 X 27 X 128의 이미지를 3 X 3 (stride =2) 으로 Max Pooling 2 X (13 X 13 X 128)

3. Convolution 3 Layer (GPU connection)

- 13 X 13 X 256의 이미지를 384개의 3 X 3 X 256의 필터로 Convolution(stride = 0, Padding =1) = 13 X 13 X 384

4. Convolution 4 Layer

- 2개의 (13 X 13 X 192)의 이미지를 각 각 192개의 3 X 3 X 192의 필터로 Convolution(stride = 0, Padding =1) = 2 X (13 X 13 X 192)

5. Convolution 5 Layer

- 2개의 (13 X 13 X 192)의 이미지를 각 각 128개의 3 X 3 X 192의 필터로 Convolution(stride = 0, Padding =1) = 2 X (13 X 13 X 128)

- 13 X 13 X 128의 이미지를 3 X 3(stride =2)로 Max Pooling = 2 X (6 X 6 X 128)

6. FC

- 2 X (6 X 6 X 128) = 9216을 Flatten 후 4096개의 뉴런과 FC를 하여 계산한다.

7. FC

- 4096개의 뉴런을 4096개의 뉴런과 FC를 하여 계산한다.

8. FC

- 4096개의 뉴런을 1000개의 뉴런과 FC한 후, softmax 함수를 사용 1000개의 클래스에 대한 값으로 나타낸다.


### Results

ILSVRC - 2010              

  ![2010](https://user-images.githubusercontent.com/69898343/118216162-f0f71a80-b4ad-11eb-98bc-95368ae21e5c.png)

- 2010년의 ILSVRC에 적용한 결과

ILSVRC - 2012 

  ![2012](https://user-images.githubusercontent.com/69898343/118216401-65ca5480-b4ae-11eb-8410-d068e17589ff.png)

> 1CNN: AlexNet

> 5CNNs: 5개의 비슷한 CNN 모델들의 error의 평균

> \* :pre-trained

> 7CNNs: 2011로 pre-trained된 CNN 2개 + 5CNNs (총 7개 모델의 error의 평균)


    - ILSVRC-2012 15.3%의 Error로 2위와의 10.9%의 격차로 우승!

