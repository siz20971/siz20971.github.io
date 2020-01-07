---
layout: post
title: Synology git server 설치하다가 ssh 접속이 안되는 문제가 터졌다
categories: Log
---

발생의 개요는 대충 이러하다..

1. 개인용 git server가 가지고 싶어서 git server 구축
2. 그런데 gitlab을 설치할수 없는 구형 기종이라 (ds214play) 커맨드라인으로만 하다가
3. Gogs 라는 설치형 git 패키지가 있어서 설치
4. https로는 되는데 ssh로는 clone 부터가 안된다
5. 그래서 Gogs config에서 built-in ssh server를 활성화.

이까지는 ssh 접속이 잘 되다가

6. 재부팅 후 Gogs 서버가 제대로 켜지고, ssh로 git 접근이 되는걸 확인
7. **그런데 시놀로지 본체에 ssh 접속이 안된다.**

이후 여러가지 삽질을 했는데 결국 전부 실패하고, 시놀로지에 문의를 넣은 상태 ㅡㅡ;

![ssh_refused](/images/20200108_ssh_refused.PNG)

`log에는 ssh service가 분명히 시작되었다고 하는데 접속이 안된다`

삽질한게 아까워서 로그라도 남긴다.

일단 ssh 접근이 안되니, sshd_config 같은 파일을 확인해볼수가 없어서 telnet을 열었다.

telnet은 애초에 바뀔 일이 없으니 잘 접속되어서 다행이었다.

netstat으로 열린 포트를 확인해보니 확실히 22는 없었다.

![sshd_status](/images/20200108_sshd_status.PNG)

검색해보니 synoservicectl 명령어로 sshd를 재실행시킬수 있다고 해서 시도해봤지만 stop/waiting 상태를 벗어나지 못했다.

--reload 해봐도 안되고...


일단 삽질은 결국 해결 안된채로 끝났고, 해결법을 찾거나 하면 기록차원해서 추가 포스팅을 해야할 것 같다.