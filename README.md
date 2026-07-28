# 개발 워크스테이션 구축 미션

## 1. 프로젝트 개요
터미널, Docker, Git/GitHub를 직접 세팅하고, 누구의 컴퓨터에서든
같은 방식으로 재현되는 개발 환경을 만드는 것이 목표다.
웹 서버를 컨테이너로 만들어 포트 연결·폴더 연결·데이터 보존까지
실행 결과로 검증하고, 전 과정을 이 문서에 기록했다.
(← 이 두 문장은 제출 전에 본인 말투로 한 번 고쳐 쓰세요)

## 2. 실행 환경
- OS: (← 9단계에서 채움)
- Shell: (← 9단계에서 채움)
- Docker: (← 9단계에서 채움)
- Git: (← 9단계에서 채움)
- 비고: 캠퍼스 보안 정책상 sudo 제한 → OrbStack으로 Docker 엔진 구동

## 3. 수행 체크리스트
- [ ] 터미널 기본 조작(위치/목록/이동/생성/복사/이름변경/삭제/내용확인/빈파일)
- [ ] 권한 변경 실습(파일 1개 + 디렉토리 1개, 전/후 비교)
- [ ] Docker 설치·데몬 점검(version / info)
- [ ] Docker 운영 명령(images / ps / ps -a / logs / stats)
- [ ] hello-world 및 ubuntu 컨테이너 실습(종료/유지 차이 정리)
- [ ] Dockerfile 직접 작성 → 커스텀 이미지 빌드/실행
- [ ] 포트 매핑 접속 증거(2개 포트, 주소창 포함)
- [ ] 바인드 마운트 변경 전/후 비교
- [ ] 볼륨 영속성(컨테이너 삭제 전/후) 검증
- [ ] Git 설정(git config --list) + GitHub·VSCode 연동
(← 각 단계 끝날 때마다 [ ]를 [x]로 바꾸세요)

## 4. 수행 로그

### 4-1. 터미널 기본 조작

```
ljh9512060277@c4r5s5 CodysseyMissionE1 % pwd
/Users/ljh9512060277/CodysseyMissionE1
ljh9512060277@c4r5s5 CodysseyMissionE1 % ls -la
total 8
drwxr-xr-x   4 ljh9512060277  ljh9512060277   128  7 28 13:34 .
drwxr-x---+ 25 ljh9512060277  ljh9512060277   800  7 28 13:40 ..
drwxr-xr-x  15 ljh9512060277  ljh9512060277   480  7 28 13:40 .git
-rw-r--r--   1 ljh9512060277  ljh9512060277  3162  7 28 13:39 README.md
ljh9512060277@c4r5s5 CodysseyMissionE1 % mkdir images
ljh9512060277@c4r5s5 CodysseyMissionE1 % mkdir practice
ljh9512060277@c4r5s5 CodysseyMissionE1 % cd practice
ljh9512060277@c4r5s5 practice % touch memo.txt
ljh9512060277@c4r5s5 practice % cat memo.txt
ljh9512060277@c4r5s5 practice % cp memo.txt memo_copy.txt
ljh9512060277@c4r5s5 practice % mv memo_copy.txt note.txt
ljh9512060277@c4r5s5 practice % ls - la
ls: -: No such file or directory
ls: la: No such file or directory
ljh9512060277@c4r5s5 practice % ls -la
total 0
drwxr-xr-x  4 ljh9512060277  ljh9512060277  128  7 28 13:45 .
drwxr-xr-x  6 ljh9512060277  ljh9512060277  192  7 28 13:45 ..
-rw-r--r--  1 ljh9512060277  ljh9512060277    0  7 28 13:45 memo.txt
-rw-r--r--  1 ljh9512060277  ljh9512060277    0  7 28 13:45 note.txt
ljh9512060277@c4r5s5 practice % rm note.txt
ljh9512060277@c4r5s5 practice % cd ..
ljh9512060277@c4r5s5 CodysseyMissionE1 % cd /tmp && pwd
/tmp
ljh9512060277@c4r5s5 /tmp % cd - 
~/CodysseyMissionE1
ljh9512060277@c4r5s5 CodysseyMissionE1 % cd ./practice && pwd && cd
/Users/ljh9512060277/CodysseyMissionE1/practice
```

"절대 경로는 /부터 시작하는 전체 주소, 상대 경로는 현재 위치 기준의 길 안내다. /tmp는 어디서 쳐도 같은 곳으로 가지만 ./practice는 서 있는 위치에 따라 결과가 달라진다."

### 4-2. 권한 실습 (전/후 비교)
```
ljh9512060277@c4r5s5 CodysseyMissionE1 % echo 'echo "실행 성공!"' > hello.sh 
ljh9512060277@c4r5s5 CodysseyMissionE1 % ls -1 hello.sh
hello.sh
ljh9512060277@c4r5s5 CodysseyMissionE1 % ./hello.sh 
zsh: permission denied: ./hello.sh
ljh9512060277@c4r5s5 CodysseyMissionE1 % chmod 755 hello.sh
ljh9512060277@c4r5s5 CodysseyMissionE1 % ls -1 hello.sh
hello.sh
ljh9512060277@c4r5s5 CodysseyMissionE1 % ./hello.sh
실행 성공!
ljh9512060277@c4r5s5 CodysseyMissionE1 % mkdir secret 
ljh9512060277@c4r5s5 CodysseyMissionE1 % ls -1d secret
secret
ljh9512060277@c4r5s5 CodysseyMissionE1 % chmod 644 secret
ljh9512060277@c4r5s5 CodysseyMissionE1 % cd secret
cd: permission denied: secret
ljh9512060277@c4r5s5 CodysseyMissionE1 % chmod 755 secret
ljh9512060277@c4r5s5 CodysseyMissionE1 % cd secret && cd .. 
ljh9512060277@c4r5s5 CodysseyMissionE1 % rm - r secret 
rm: -: No such file or directory
rm: r: No such file or directory
rm: secret: is a directory
ljh9512060277@c4r5s5 CodysseyMissionE1 % rm -r secret
```

### 4-3. Docker 설치·점검
(← 5단계 앞부분 로그)

### 4-4. 컨테이너 실습 (hello-world / ubuntu / 종료·유지 관찰)
(← 5단계 뒷부분 로그 + 관찰 정리 문장)

### 4-5. 커스텀 이미지 (Dockerfile)
- 선택한 기반: nginx:alpine (경량 웹 서버)
- 커스텀 포인트와 목적:
  1) LABEL — 이미지에 이름표를 붙여 식별 가능하게 함
  2) ENV — 환경 변수를 심어 설정과 코드를 분리하는 구조를 연습
  3) COPY — 기본 페이지를 내 정적 페이지로 교체
(← 6단계 빌드/실행 로그 붙여넣기)

### 4-6. 포트 매핑 접속 증거
(← curl 출력 + 캡처 삽입)

### 4-7. 바인드 마운트 / 볼륨 영속성
(← 7단계 로그 + 전/후 캡처)

### 4-8. Git 설정 및 GitHub·VSCode 연동
(← 8단계 로그 + 연동 캡처)

## 5. 검증 방법 요약
| 확인 대상 | 사용한 명령/방법 | 증거 위치 |
|---|---|---|
| Docker 데몬 동작 | docker info | 4-3 |
| 컨테이너 상태 | docker ps / ps -a | 4-4 |
| 웹 접속 | curl + 브라우저 | 4-6, images/ |
| 마운트 반영 | 파일 수정 후 새로고침 | 4-7, images/ |
| 볼륨 영속성 | 컨테이너 삭제 후 cat | 4-7 |
| GitHub 연동 | push 후 커밋 목록 확인 | 4-8, images/ |

## 6. 트러블슈팅
### 사례 1:
- 문제(오류 문구 원문):
- 원인 가설:
- 확인(사용한 명령):
- 해결/대안:

### 사례 2:
- 문제:
- 원인 가설:
- 확인:
- 해결/대안:docs: README 뼈대 작성