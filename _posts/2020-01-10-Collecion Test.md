---
layout: post
title: Synology SSH Service 초기화
categories: Synology,ssh
---

1. Synology Telnet service 활성화 후 admin 계정으로 접속
2. `sudo -i` 로 root 권한으로 변경
3. /etc/ssh/sshd_config 파일을 초기화 해야함.
   1. mv /etc/ssh/sshd_config /eetc/ssh/sshd_config.bak 기존 config 백업
   2. cp -a /etc.defaults/ssh/sshd_config /etc/ssh/sshd_config 로 초기화
4. DSM 에서 SSH Service 다시 활성화

/etc.defaults/ 폴더에 기본값들이 있음을 기억해둬야겠다.