---
title: AI와 함께 작곡한다면?
category: Data Analysis
tags: [Creative AI, Data Analysis]


---

AI를 통해 사람의 창의성을 발현 시키는 것은 가능한 일일까?

------

이것이 가능한지, 또 옳은일인지에 대해 다양한 의견이 있고 서로의 입장이 워낙 견고하다. 듣고 보는 것만으로는 그 정도를 가늠하기가 어려워서 이 질문을 나에게 적용해보기로 했다. 

**예술가가 되기 - 인생의 어느 작은 구석에서라도 예술성을 실현하며 살기**. 

"인생의 어느 작은 구석에서라도 예술성을 실현하기"는  내 인생 모토 중 큰 부분을 차지하는 것이기도해서 좋은  실험 기회가 되었다. 내가 도전한 부분은 AI를 통해 작곡(편곡)하기였다. 타겟 곡은 평소 좋아하던 Pudditorium의 If I could meet again이라는 음악이다. 반복적인 멜로디에 여러 악기가 덧입혀지면서 분위기가 고조되고 독특한 분위기를 풍기는 곡인데, 피아노로 따라치면 그 분위기는 모두 없어지고 선율만 남아 아쉬움이 컸다. 내가 표현할 수 없었던 피아노 이외의 부분을 AI로 표현하면 좋을 것 같아 이 곡을 선정하게 되었다.

원곡을 들어보자 (클릭하면 재생)

[![푸디토리움](http://img.youtube.com/vi/GLWd6o8vwvw/0.jpg)](http://www.youtube.com/watch?v=GLWd6o8vwvw "푸디토리움")

아름답다. 리더 김정범 씨가 어머니를 생각하며 만든 곡이라고 한다. 

이 훌륭한 곡을 잘 들어보면 아래 멜로디 라인이 반복됨을 알 수 있다. (이렇게 간단한 멜로디에 아름다운 음악이라니!) 

![sheet](/assets/img/sheet.png)

이 멜로디를 토대로 구글 마젠타 스튜디오를 이용해서 내가 혼자 연주할 때보다 업그레이드 된 새로운 느낌의 곡을 만들어보았다.

마젠타로 재현한 음악 (클릭하면 재생)

[![magenta](http://img.youtube.com/vi/39Fp2y1749w/0.jpg)](http://www.youtube.com/watch?v=39Fp2y1749w "magenta")



위 곡을 만들기 위해 사용한 Magenta Studio는 아래 사이트에 가면 확인해 볼 수 있다. Magenta Studio는 machine learning을 이용해서 music generation을 가능하게 하는 open source tool이다. 이것은 standalone으로도 동작하고 ableton DAW 상에서 plugin 방식으로도 사용된다.

구글 마젠타 스튜디오 : https://magenta.tensorflow.org/studio

![magenta](/assets/img/magenta.png)

내가 위 곡을 만들기 위해 진행한 과정은 다음과 같다.

1. 8마디 피아노 + 8마디 스트링 (직접 연주) 

   곡의 뼈대를 만들기 위해 위 악보에 채보되어 있는 멜로디 라인을 처음에는 피아노, 그 다음에는 스트링을 이용해서 직접 연주

2. Magenta Drumify : Drum bit Generation

   곡에 드럼라인을 입히기 위해 Drumify를 이용해서 비트 생성

3. Magenta Arpeggio Generation

   https://codepen.io/teropa/full/ddqEwj/

   위 사이트에서 제공하는 아르페지오 생성기를 사용

4. Magenta Continue : Bass Solo Generation 

   멜로디를 input으로 하여 그에 맞는 bass solo 라인 생성 (2개 종류)

5. Magenta Interpolate : Additional Bass Solo 

   4번에서 생성된 2개 bass solo를 input으로 받아 이 둘 라인을 자연스럽게 연결해 줄 수 있는 additional bass solo 생성



------

단상

곡을 만들기 위해 Magenta Studio를 이용해서 여러 시도를 해보고 조합하는데에는 시간이 별로 들지 않았다. 15분 정도, 충분힌 시간을 가지고 다양한 시도를 한다면 더 멋진 곡이 나올수도. 하지만 Ableton DAW를 처음 접해보는지라, 기능 숙지하는데에 시간이 오히려 더 많이 걸렸다. 또한 사람이 작곡하는 경우에는 멜로디 라인에 따라 의도하는 화성, 감정선이 있을건데 Magenta를 이용해서 generation을 해서 끼워넣을 경우 그 감성이 깨지는 듯한 인상도 받았다. 따라서 짧은 멜로디 진행에 더 잘 어울릴 것 같다. 그 밖에 자신이 주로 연주하는 악기 외에도 다양한 악기를 나의 input에 맞게 생성해 볼 수 있으니 창작자에게 좋은 seed가 될 수 있을 것이라고 생각한다. 마젠타에 흥미로운 주제의 프로젝트가 많으니 시도해본다면 시간가는 줄 모를 것이다. 

AI를 이용해 사람의 창의성을 발현 시키는 것이 가능한 일일까? 처음 했던 이 질문에 대한 대답을 한다면, Absolutely, Yes. 다만 그것을 인간이 허용할지 안할지에 대한 의사결정이 필요할 뿐...

(참고영상)

[![ArtAI](http://img.youtube.com/vi/Sbd4NX95Ysc/0.jpg)](http://www.youtube.com/watch?v=Sbd4NX95Ysc "ArtAI")