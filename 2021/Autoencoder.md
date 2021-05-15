# Autoencoder
* KAIST, 숙명여대 강남우 교수님 강의를 듣고 정리한 내용입니다.  
<p align = "center"><img src = "https://user-images.githubusercontent.com/83944915/118348022-646a5c00-b582-11eb-97a4-329be8092fd5.png"></p>  

* Autoencoder는 encoding, decoding을 이용하여 고차원 → 저차원 → 고차원으로 압축 및 복원을 하는 방법이다.  

+ encoding을 통해 고차원 → 저차원으로 압축할 수 있으며 이로 인해 neural network 중간에 Bottleneck Hidden Layer가 생긴다.  

+ decoding을 통해 저차원 → 고차원으로 복원할 수 있으며 Input data와 거의 동일한 Output data를 얻을 수 있다.  

<br><br>  
# Autoencoder를 이용한 비지도학습

<p align = "center"><img src = "https://user-images.githubusercontent.com/83944915/118348160-5ff27300-b583-11eb-9ec1-fa83d7d653ea.png"></p>  
<p align = "center"><img src = "https://user-images.githubusercontent.com/83944915/118348365-ba400380-b584-11eb-929b-44acff5c429e.png"></p>  

+ Autoencoder는 압축, 복원 과정을 거치며 나타나는 Loss에 집중한다.  

+ Autoencoder는 입출력이 동일한 네트워크이므로 비지도학습문제를 지도학습문제로 바꾸어서 해결하는 독특한 특징(지도학습처럼 보이는 비지도학습)을 가진다.  

+ Autoencoder 자체 특성으로 Encoder에 집중되어 있으므로 Decoder의 성능이 떨어지는 편이다.  

+ Input data와 Output data간의 차이가 Loss로 나타나게 되며 Anomaly Detection에서 이를 이용해 이상신호를 감지한다.
<p align = "center"><img src = "https://user-images.githubusercontent.com/83944915/118348465-7ef20480-b585-11eb-9de5-20c2f2fa8f47.png"></p>  
<p align = "center"><img src = "https://user-images.githubusercontent.com/83944915/118348520-d7c19d00-b585-11eb-8a08-fcc54b8e2c8f.png"></p>  
<p align = "center"><img src = "https://user-images.githubusercontent.com/83944915/118348526-ee67f400-b585-11eb-9b63-859611dfc3d8.png"></p>

+ 왼쪽과 같이 Encoder와 Decoder를 이용해 학습시킨이후, Decoder 부분을 제거하고 classifier로 대체하여 Loss function을 확인한다.  

+ 오른쪽과 같이 Encoder는 지도학습의 첫 부분으로 사용될 수 있다.


# Anomaly Detection by Autoencoder
<p align = "center"><img src = "https://user-images.githubusercontent.com/83944915/118348578-4ef73100-b586-11eb-8735-26b74707b2dd.png"></p>  

+ 정상 data만 Autoencoder로 학습시키면 Normal input data에서는 Autoencoder를 거친 후 제대로 복원이 되지만 Abnormal input data의 경우 복원이 제대로 되지 않는다.  

+ 기존 data와 복원 data의 차이 값을 Reonstruction error라 부르고 Anomaly Detection의 주요 변수로 작용한다.  

+ Anomaly Detection에서 Reconstruction error가 임계치보다 크면 Abnormal로 분류한다.
