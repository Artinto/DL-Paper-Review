# Deep Residual Learning for Image Recognition

## Abstract

- Deep neural networks일수록 더 성능이 잘 나와야 하나 그렇지 못하였다.
- 이를 학습과정에서 residual(잔차)학습 개념을 도입
- 함수를 새로 만드는 방법 대신에 residual function, 잔차 함수를 learing에 사용하는 것으로 layer를 재구성
- VGG보다 8배 깊은 152 layers
###  결과
- ILSVRC-2015에서 3.57%의 error rate을 보이며 우승
<br><br>

## Introduction
![graph](https://user-images.githubusercontent.com/69898343/119967227-170cd680-bfe7-11eb-954d-241917eab4aa.png)

를 제시하며 Layer가 많아질수록 학습이 더 잘되지 못 하는 점에 의문을 가짐.
- 이 논문에서 다루는 문제는 Degradation Problem(network가 깊어질수록 accuracy가 떨어지는 문제)
- Overfitting의 경우 Train 자체의 에러는 적어야 하나, 이는 Train Error도 높아 Overfitting과 다른 문제라고 분류.
- Residual mapping을 통해 degradation의 문제를 해결 가능하다.

![KakaoTalk_20210528_144849074](https://user-images.githubusercontent.com/77203609/119936366-05660780-bfc4-11eb-80f3-17645786083c.png)

- 기존의 networks를 H(x)라 한다면, F(x) = H(x) - x, 즉 출력과 입력간의 차(residual mapping)에 대해 학습을하면 
  degradation의 문제를 해결 가능하다고 제시
- H(x) = F(x) + x이므로 identity mapping을 수행하며, Shortcut Connections라고 불리는 방법이다.

## Deep Residual Learning

### Residual Learning
- H(x) : 기존의 networks
- x : inputs
- F(x) : H(x) - x
- Stacked layer들이 H(x) 대신 residual function인 F(x)를 approximate 하는 것이 학습을 더 수월하게 한다.

### Identity Mapping by Shortcuts
- building block은 y = F(x, {Wi}) + x
- x, y: input and output vectors of the layers
- F(x, {Wi}): residual mapping to be learned
- 이때 F와 x의 차원이 같지 않으면 linear projection(Ws)을 수행해 차원을 맞춰준다.
- 따라서 y = F(x, {Wi}) + Wsx

### Network Architectures(Plain Network , Residual Network)
![KakaoTalk_20210528_152214069](https://user-images.githubusercontent.com/77203609/119939416-d0a87f00-bfc8-11eb-9ac0-0545b0f55a83.png)

- 가운데는 VGG기반 Plain Network, 오른쪽은 Plain Network에 기반한 Residual Network
1. Plain Network
  - Conv layers는 대부분 3X3 filters를 가지고 있다.
  - 각 layers는 같은 크기의 output feature map을 가지고, 같은 수의 filters를 갖는다.
  - feature map size가 반으로 줄어들었다면, time-complexity를 유지하기 위해 filters의 수는 두 배가 된다.
  - downsampling을 하기 위해서 stride가 2인 conv layers를 통과시킨다. 
  - 마지막엔 global average pooling과 1000-way-fully-connected layer with softmax
  - VGG에 비해  filter의 수가 적고 lower complexity를 가진다는 것이 장점
 
2. Residual Network
  - Plain network에 기반하여, shortcut connections를 추가한 residual version ResNet
  - Identity Shortcut은 input과 output을 같은 차원으로 맞춰줘야 하는데 차원이 증가하면 두 가지 방법을 사용한다.
    1. zero padding, 추가 파라미터는 없음
    2. linear projection
  - 위 두 가지 방법은 shortcut이 feature map을 2씩 건너뛰기 때문에 stride를 2로 사용한다.
 
### Implementation
-  ImageNet data에 적용
-  image resized 224 * 224
-  Batch normalization을 Convolution 후에 바로 적용
-  SGD, mini batch 256
-  learning rate는 0.1에서 시작해서 error가 반복될 떄 마다 줄여나간다.
-  weight decay 0.0001, momentum 0.9, no dropout
-  test에는 10-crop testing 적용

![graph1](https://user-images.githubusercontent.com/69898343/119969279-52100980-bfe9-11eb-9bf0-e62596d16855.png)

그 결과 다음과 같이 Error가 낮아져 Layer가 많을 때 학습이 용이하다는 것을 나타낸다.
또한, 수렴 속도 또한 향상된 것을 볼 수 있다.
추가적으로, Plain의 그래프에서 Layer가 학습률이 떨어지는 것을 vanishing gradient 문제가 아닌 low convergence rates(낮은 수렴률)인 것을 추가적으로 알아내었다.

![graph2](https://user-images.githubusercontent.com/69898343/119969870-0f9afc80-bfea-11eb-82b5-454eea85f434.png)

다음과 같은 Bottleneck 구조에서는 identity mapping이 더욱 효과적으로 Parameter를 줄이고, 연산을 줄일 수 있음을 암시한다.

### CIFAR-10의 학습
![graph3](https://user-images.githubusercontent.com/69898343/119973060-f3995a00-bfed-11eb-8c12-5617189876aa.png)
과 같이 그림의 수가 32 X 32로 작기에, Layer를 줄여서 학습을 시도하였고, 


![graph4](https://user-images.githubusercontent.com/69898343/119973324-4c68f280-bfee-11eb-8888-e07c453a410d.png)

결과는 다음과 같다.
   
