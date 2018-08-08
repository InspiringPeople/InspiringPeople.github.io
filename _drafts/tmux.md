---
category : Data Analysis
title : tmux로 jupyter notebook 끊기지 않고 수행하기
tags : [Data ANalysis, tips, tmux]
---  



로컬 PC에서 서버에 원격 접속하여 모델링 등 긴 시간 작업 수행하는 경우(5시간 이상 돌려야 하는 모델링이 대부분…) 
network 끊기면 작업 중단 되어 인데 다음날 아침에 와서 폭발! -.-)하는 경우가 많은데,
이런 애로사항을 문의하니 홍근씨가 TMUX라는게 있어요!라며, 바쁜 시간을 쪼개서 아래 내용을 공유해주었습니다.
개발자분들이라 대부분 아실듯하지만, 그래도 공유드려봅니다 ㅎㅎ (사실 모델링 쪽 분들이 더 유용할 듯하네요)




tmux란? (from wiki)
============================
tmux는 사용자가 단일 단말기 창 또는 원격 터미널 세션 안에서 여러 별도의 터미널 세션에 액세스할 수 있도록 여러 가상 콘솔을 다중화하는데 사용할 수 있는 응용 소프트웨어이다.
============================

tmux command
============================
# 새 세션 생성
$ tmux new -s <session-name>

# 세션 이름 수정
ctrl + b, $

# 세션 종료
$ (tmux에서) exit

# 세션 중단하기 (detached)
ctrl + b, d

# 세션 목록 보기 (list-session)
$ tmux ls

# 세션 다시 시작
$ tmux attach -t <session-number or session-name>
============================

사용방법
============================
run.sh 파일 안에 오래 걸리는 command set 정의하기
chmod +x run.sh : script 파일 안에 있는 커맨드를 수행하기 위한 permission 부여
./run.sh : 실행
이후 며칠이 걸리더라도, 로컬 PC network이 갑자기 끊겨도 저의 모델링은 잘 돌아가고 있는 중입니다. ㅎㅎ
============================
