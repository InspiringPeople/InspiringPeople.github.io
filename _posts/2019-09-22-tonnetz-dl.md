---
title: tonnetz 음악이론을 딥러닝에 접목시키기
category: Data Analysis
tags: [Creative AI, Data Analysis]




---

음악 이론에서 쓰이는 tonnetz를 딥러닝에 접목시킨 논문을 소개한다. 

논문 제목 : “Modeling Temporal Tonal Relations in Polyphonic Music through Deep Networks with a Novel Image-Based Representation” (AAAI2018)

논문 구현 : https://github.com/ChinghuaChuan/tonnetzDL

tonnetz란 무엇일까?

(1) tonnetz (by Euler)

1739년 오일러에 의해 처음 기술된 것으로 tonality와 tonal space의 관계를 graphical한 방식으로 표현한 것

![image-20190923002102786](/Users/ykjang/Library/Application Support/typora-user-images/image-20190923002102786.png)

- 하나의 노드는 12 pitch들과 연결
- 오른쪽은 완전 5도, 왼쪽은 완전 4도
- 3개의 노드는 3도화음으로 세모 모양 구성 (위 세모는 minor, 아래 세모는 maj 코드)

이렇게 tonalty를 geographical 하게 표현하면 어떤 점이 좋을까? 바로 음악에 숨어 있는 패턴이라던가, 관계 등을 시각적으로 잘 확인할 수 있다는 것이다. 다음 영상은 Gymnopedie No.1 곡을 tonnetz로 표현한 것이다. 

https://www.youtube.com/watch?v=nidHgLA2UB0

기존에 AI를 이용한 멜로디 생성 등과 같은 music generation은 많이 연구되어 왔지만, 이미 오랜 세월동안 음악가들이 완성해 놓은 music theory를 deep learning에 녹여내는데 부족함이 있었다. 위와 같이 tonnetz를 이용하면 음악 도메인의 지식을 deep learning에 반영할 수 있는 방법이 생기는 것이다.

논문은 tonnetz embedding, model architecture 순으로 설명하도록 하겠다.

(1) extended tonnetz embedding

위에 소개된 오일러에 의한 tonnetz가 아닌 extended tonnetz가 본 논문에서 소개되었다.

기존의 삼각형 관계를 사각형으로 바꾸 것인데, 임베딩 matrix로 바꾸기 위함으로 해석된다. 각 선들 (세로, 가로, 왼쪽, 오른쪽)은 기존의 tonnetz처럼 tonalty 특성을 반영한다.

![image-20190923002410842](/Users/ykjang/Library/Application Support/typora-user-images/image-20190923002410842.png)

- 오른쪽과 완전 5도 관계
- C4 than G3 is closer to C4 than G4 (--> 이부분은 잘 이해가 되지 않는다)
- 세로축은 장3도 관계 (common tonnetz에서는 빗금선)
- 가로축은 데이터의 pitch range (여기서는 24 rows, C0 to C#8)
- polyphonic music에서 time slice 동안 여러 음정이 동시에 연주되는 것을 모델링 할 수 있게 함

(2) network architecture

딥러닝 모델은 2단계로 구성된다. 

첫 번째는 two-layered convolutional autoencoder로 아래 그림 (a)와 같다.

- autoencoder의 input은 tonnetz matrix embedidng 값이 됨
- 처음 encoder 부분은 tonnetz의 abstract representation을 반영하고, decoder 부분에서는 input과 가장 근접한 값으로 디코딩된다.
- loss function은 encoding된 X와 decoding된 X'의 sigmoid cross entropy

![image-20190923003917554](/Users/ykjang/Library/Application Support/typora-user-images/image-20190923003917554.png)

두 번째는 three-layered LSTM network이다.

- 앞의 CNN autoencoder의 output 값이 LSTM network의 입력 값으로 제공
- vanishing gradient problem 방지를 위해 LSTM (CEC, constant error carousel) 사용
- 3 hidden layer, sigmoid loss function

