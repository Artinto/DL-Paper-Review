# All you need is Attention `6 Dep 2017`

## Abstract 

Recurrent한 모델을 사용하지 않고서 Seq2Se2 구조를 어느정도 따르며 Attention만으로 모델을 고안하였다.<br>
결과적으로 현재 state of the art 모델들 보다 성능이 우수하며 *병렬화가 가능하다*는 큰 장점을 가진다.
  - Recurret 한 모델들은 Sequential한 특징 때문에 병렬화가 불가능했다.
  - Sequence가 길어질수록 병렬화의 필요성은 더욱 커진다. 
    - Sequence가 길어짐에 따라 입출력 간 단어의 간 길이가 길어질수록 Dependency 문제가 발생하는데 이는 Attention 기법을 통해 해결할 수 있었다.
      - Attention : Decoder에서 출력 단어를 예측하는 매 Step마다 Encoder에서의 전체 입력 정보를 한 번 더 참고하는 방법<br> 다만 전체 입력 정보를 다 동일 비율로 참고하는 것이 아니라 해당 시점에서 특별히 더 주목해야할 부분을 더 집중해서 참고하게 된다. 

## Introduction 

Transformer는 Sequential한 데이터를 처리하는 기존 모델들의 병렬화 문제를 해결하기 위해 고안되었음
NLP에서 큰 도약이었던 Attention 기법 조차 모두 Recurrent한 모델들과 같이 사용되고 있었으며, 
