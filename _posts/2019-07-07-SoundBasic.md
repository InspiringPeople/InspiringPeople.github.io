---
title: 사운드 관련 기본
category: Writing
tags: [sound, sound design, Writing]


---

요새 사운드 관련 작업을 하면서 기초지식이 너무 부족하다고 판단되어 관련 책들을 읽어보는 중이다. 아래는 **사운드 디자인을 위한 맥스 (김영민 지음)** 책 내용을 요약/발췌한 것이다.

- 맥스란?

  Max는 Object를 기반으로 알고리즘, 사운드 영상, 피지컬 컴퓨팅 등 다양한 미디어를 통합하여 새로운 미디어 예술 작품을 만들 수 있도록 도와주는 비주얼 프로그래밍 언어이다. Miller Puckette이 1980년대 컴퓨터를 이용하여 전자음악을 컨트롤 하기 위해 처음 제작된 맥스는 컴퓨터 음악의 아버지라 불리우는 Max Mathews의 이름을 착안하여 만들어졌다.

  

- Max / MSP / Jitter

  MSP (Max Signal Prcessing)는 사운드 영역으로 MIDI 시스템과의 결합과 조절, 다양한 형태의 소리합성(Sound Synthesis), 사운드 프로세싱(Digital Sound Processing), 그리고 이를 위한 실시간 컨트롤 시스템 제작이 가능하다.

  Jitter는 영상 시스템 영역으로 디지털 Generating, 촬영 영상의 Processing 그리고 이를 위한 실시간 컨트롤 시스템 제작이 가능하다.

  Max는 MSP와 Jitter의 연결과 이들을 컨트롤 하기 위한 알고리즘 영역을 담당한다. 각 MSP와 Jitter 영역은 데이터 시그널의 종류가 달라 서로 직접적인 연결이 거의 불가능하다. 이를 연결하고 전반적인 시스템의 구조를 알게 해주는 역할을 Max가 하고 있다.

- Argument / Message / Number / Attribute

  오브젝트는 역할을 가지고 업무를 수행해 낸다. 어떠한 일을 수행하기 위해 오브젝트에 필요한 것은 그 일을 수행하기 위한 데이터다.

  - Argument : 인수, 오브젝트가 보유하고 있는 기본적인 정보
  - Attribute : 속성, 오브젝트가 일을 수행하기 위한 성질
  - Message : 오브젝트가 일을 하기 위해 필요한 명령
  - Number : 오브젝트가 일을 수행하기 위해 필요한 데이터

- Instruction 관련 오브젝트

  알고리즘 운영의 시작과 멈춤, 그리고 오브젝트 업무 수행을 제어한다.

  - Button (Bang) : 버튼이 활성화되면 밑으로 연결되어 있는 오브젝트 혹은 데이터 업무를 수행한다. (수행 시 노란색으로 색상 바뀜)
  - Toggle : 오브젝트가 일을 수행할 수 있도록 하는 전원 개폐 장치라고 할 수 있다. On/Off (1,0)으로 처리
  - Q : Button/Toggle 실습 예제 실행시키는 방법(실행모드), 둘의 차이

- Data Types

  - Number Box : int/float type & inspector (display format & value)

  - Message Box : 텍스트 & 숫자 데이터 처리

    Char / List 데이터 형태를 처리

    모든 오브젝트와 Button / Toggle과 같은 명령 오브젝트는 숫자 / 텍스트를 입력 / 출력 할 수 있다.

  - Char : 하나의 데이터

  - List : 여러 개의 데이터가 하나의 Pack으로 묶여 있는 형태, 여러 개의 데이터를 동시에 처리할 때 사용. 메세지 박스에서는 띄어 쓰기로 표현, 또는 Pack 오브젝트로 만들 수 있음

  - Multiple Char : 하나의 메세지 박스 안에 여러 개의 Char Type 데이터 입력, List Type과 달리 콤마(,)로 구분

- Q : Mathematics 예제 실행해보기

- Q : 궁금한 명령어 찾는 방법 helper, online tutorials

  - muse port에서 p OUT, p .., nested관계?

    

- 알고리즘 제어를 위한 MIDI 사용

  Max는 MIDI 신호를 입력으로 받을 수 있고, 알고리즘에 의하여 생성된 데이터를 MIDI 신호로 변환시켜 출력 시킬 수 있다. 또한 미디 컨트롤러를 통하여 실시간 조절이 가능하게 할 수 있으며 다양한 H/W, S/W 사이에서 데이터를 송수신 할 수 있다.

  - MIDI (Musical Instruments Digital Interface) 컴퓨터 음악을 위한 통신규격
  - Q : Max MIDI Setup 메뉴에 아무것도 없음
  - Q : Max - Extra - Miditester / Audiotester 사용방법

- Notion / noteout

  건반을 누르면 내부적으로는 '눌렀다'와 '놓다' 두 가지 행위를 한 것이므로 데이터가 두번 출력된다. (noteoff 시에 velocity 0값)

  notein / noteout 오브젝트는 마스터 건반을 눌렀을 때 관련 미디 데이터를 입력 받을 수 있도록 하는 오브젝트, pitch, velocity, channel을 입력/출력 받는다.

- ctlin

  미디 컨트롤러에 연결된 노브나 페이더 데이터를 입력 받고 싶을 때 사용, CC (Control Change) List 데이터를 입력 받는 오브젝트 (Modulation, Foot Control, Expression, Breath Control 등) 

  0-127까지의 범위 안에서 조절되어 음악의 생기를 불어넣어준다. 다양한 파라미터의 실시간 컨트롤을 위한 기능으로 주로 사용

  Control Value / Control Number / MIDI Channel

  Q : 미디 채널?

- stripnote

  건반을 누르고 다른 건반으로 옮길 때 noteoff 되어 벨로시티 데이터가 0으로 되지 않고 noteon 되었을 때의 벨로시티 데이터를 유지하도록 하는 오브젝트

- makenote

  알고리즘을 통해 생성된 데이터를 미디 노트 데이터로 사용하기 위해 최적화된 오브젝트

  inlet : pitch / velocity / duration

  outlet : pitch / velocity

- MIDI의 활용

  Q : 해당 사진 그림 설명

  Q : axmd는 매번 로딩해야 하나?

  