# AI/SW 개발 워크스테이션 구축

## 1. 프로젝트 개요

코딧세이(Codyssey) 과정의 첫 번째 미션으로, 개발자의 기본 도구인 **터미널(CLI)**, **Docker(컨테이너)**, **Git/GitHub(버전 관리)**를 직접 세팅하고 검증하는 프로젝트입니다.

"내 컴퓨터에서만 돌아가는" 문제를 없애고, 누구나 같은 환경에서 실행·배포·디버깅할 수 있는 **재현 가능한 개발 워크스테이션**을 구축하는 것이 목표입니다.

---

## 2. 실행 환경

| 항목 | 버전/정보 |
|------|-----------|
| OS | macOS 15.7.4 |
| Shell | zsh |
| Terminal | Terminal.app |
| Docker | Docker version 28.5.2, build ecc6942 |
| Docker Runtime | OrbStack |
| Git | git version 2.53.0 |
| Editor | Visual Studio Code |

---

## 3. 수행 항목 체크리스트

- [ ] 터미널 기본 조작 (pwd, ls, mkdir, cp, mv, rm, cat, touch)
- [ ] 파일/디렉토리 권한 변경 실습 (chmod)
- [ ] Docker 설치 확인 및 기본 점검 (version, info)
- [ ] Docker 기본 운영 명령 수행 (images, ps, logs, stats)
- [ ] hello-world 컨테이너 실행
- [ ] ubuntu 컨테이너 실행 및 내부 진입
- [ ] attach/exec 차이 관찰 및 정리
- [ ] Dockerfile 커스텀 이미지 빌드 및 실행
- [ ] 포트 매핑 접속 확인 (2회)
- [ ] 바인드 마운트 변경 반영 확인
- [ ] Docker 볼륨 영속성 검증
- [ ] Git 사용자 설정 및 GitHub 연동
- [ ] VSCode GitHub 연동 확인

---

## 4. 터미널 조작 로그

### 4-1. 현재 위치 확인

```bash
$ pwd
# (출력 결과)
/Users/codewhite77770105
```

### 4-2. 디렉토리 생성 및 이동

```bash
$ mkdir -p ~/Desktop/codyssey_E1-1/workstation/practice
$ cd ~/Desktop/codyssey_E1-1/workstation/practice
$ pwd
# (출력 결과)
/Users/codewhite77770105/Desktop/codyssey_E1-1/workstation/practice
```

### 4-3. 목록 확인 (숨김 파일 포함)

```bash
$ ls -la
# (출력 결과)
drwxr-xr-x  2 codewhite77770105  codewhite77770105  64  3 31 00:58 .
drwxr-xr-x  3 codewhite77770105  codewhite77770105  96  3 31 00:58 ..
```

### 4-4. 파일 생성 및 내용 확인

```bash
$ touch test.txt
$ echo "hello codyssey" > test.txt
$ cat test.txt
# (출력 결과)
hello codyssey
```

### 4-5. 빈 파일 생성

```bash
$ touch empty_file.txt
$ ls -la empty_file.txt
# (출력 결과 - 파일 크기가 0인 것을 확인)
drwxr-xr-x  4 codewhite77770105  codewhite77770105  128  3 31 01:55 .
drwxr-xr-x  3 codewhite77770105  codewhite77770105   96  3 31 00:58 ..
-rw-r--r--  1 codewhite77770105  codewhite77770105    0  3 31 01:55 empty_file.txt
-rw-r--r--  1 codewhite77770105  codewhite77770105   15  3 31 01:55 test.txt
```

### 4-6. 파일 복사

```bash
$ cp test.txt test_backup.txt
$ ls -la
# (출력 결과 - 원본과 사본 둘 다 존재)
drwxr-xr-x  5 codewhite77770105  codewhite77770105  160  3 31 01:56 .
drwxr-xr-x  3 codewhite77770105  codewhite77770105   96  3 31 00:58 ..
-rw-r--r--  1 codewhite77770105  codewhite77770105    0  3 31 01:55 empty_file.txt
-rw-r--r--  1 codewhite77770105  codewhite77770105   15  3 31 01:56 test_backup.txt
-rw-r--r--  1 codewhite77770105  codewhite77770105   15  3 31 01:55 test.txt
```

### 4-7. 파일 이동 / 이름 변경

```bash
$ mv test_backup.txt renamed.txt
$ ls -la
# (출력 결과 - test_backup.txt는 사라지고 renamed.txt 생성)
drwxr-xr-x  5 codewhite77770105  codewhite77770105  160  3 31 01:59 .
drwxr-xr-x  3 codewhite77770105  codewhite77770105   96  3 31 00:58 ..
-rw-r--r--  1 codewhite77770105  codewhite77770105    0  3 31 01:55 empty_file.txt
-rw-r--r--  1 codewhite77770105  codewhite77770105   15  3 31 01:56 renamed.txt
-rw-r--r--  1 codewhite77770105  codewhite77770105   15  3 31 01:55 test.txt
```

### 4-8. 파일 삭제

```bash
$ rm renamed.txt
$ ls -la
# (출력 결과 - renamed.txt 삭제 확인)
drwxr-xr-x  4 codewhite77770105  codewhite77770105  128  3 31 01:59 .
drwxr-xr-x  3 codewhite77770105  codewhite77770105   96  3 31 00:58 ..
-rw-r--r--  1 codewhite77770105  codewhite77770105    0  3 31 01:55 empty_file.txt
-rw-r--r--  1 codewhite77770105  codewhite77770105   15  3 31 01:55 test.txt
```

---

## 5. 권한 실습

### 5-1. 파일 권한 변경

**변경 전:**
```bash
$ touch permission_test.sh
$ echo '#!/bin/bash' > permission_test.sh
$ echo 'echo "Hello from script"' >> permission_test.sh
$ ls -la permission_test.sh
# (출력 결과 - 예: -rw-r--r-- 상태)
-rw-r--r--  1 codewhite77770105  codewhite77770105  37  3 31 02:14 permission_test.sh
```

**변경 후:**
```bash
$ chmod 755 permission_test.sh
$ ls -la permission_test.sh
# (출력 결과 - 예: -rwxr-xr-x 상태)
-rwxr-xr-x  1 codewhite77770105  codewhite77770105  37  3 31 02:14 permission_test.sh
```

**실행 확인:**
```bash
$ ./permission_test.sh
# (출력 결과 - "Hello from script")
Hello from script
```

> **해석:** 644(-rw-r--r--)에서 755(-rwxr-xr-x)로 변경하여 소유자·그룹·기타 모두 실행(x) 권한을 부여했다. 이로써 스크립트를 직접 실행할 수 있게 되었다.

### 5-2. 디렉토리 권한 변경

**변경 전:**
```bash
$ mkdir permission_dir
$ ls -ld permission_dir
# (출력 결과 - 예: drwxr-xr-x 상태)
drwxr-xr-x  2 codewhite77770105  codewhite77770105  64  3 31 02:16 permission_dir
```

**변경 후:**
```bash
$ chmod 700 permission_dir
$ ls -ld permission_dir
# (출력 결과 - 예: drwx------ 상태)
drwx------  2 codewhite77770105  codewhite77770105  64  3 31 02:16 permission_dir
```

> **해석:** 755(drwxr-xr-x)에서 700(drwx------)으로 변경하여, 소유자만 접근 가능하도록 제한했다. 다른 사용자는 해당 디렉토리를 열람하거나 진입할 수 없다.

---

## 6. Docker 설치 및 기본 점검

### 6-1. Docker 버전 확인

```bash
$ docker --version
# (출력 결과)
Docker version 28.5.2, build ecc6942
```

### 6-2. Docker 데몬 동작 확인

```bash
$ docker info
# (출력 결과 - 주요 부분 발췌)
Client:
 Version:    28.5.2
 Context:    orbstack
 Debug Mode: false
 Plugins:
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.29.1
    Path:     /Users/codewhite77770105/.docker/cli-plugins/docker-buildx
  compose: Docker Compose (Docker Inc.)
    Version:  v2.40.3
    Path:     /Users/codewhite77770105/.docker/cli-plugins/docker-compose
```

---

## 7. Docker 기본 운영 명령

### 7-1. hello-world 실행

```bash
$ docker run hello-world
# (출력 결과)
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

```

### 7-2. 이미지 목록 확인

```bash
$ docker images
# (출력 결과)
REPOSITORY               TAG       IMAGE ID       CREATED       SIZE
hello-world              latest    e2ac70e7319a   6 days ago    10.1kB
docker/getting-started   latest    3e4394f6b72f   3 years ago   47MB
```

### 7-3. 컨테이너 목록 확인

```bash
# 실행 중인 컨테이너
$ docker ps
# (출력 결과)
CONTAINER ID   IMAGE                    COMMAND                   CREATED        STATUS        PORTS                                 NAMES
e43ffadef6ff   docker/getting-started   "/docker-entrypoint.…"   10 hours ago   Up 10 hours   0.0.0.0:80->80/tcp, [::]:80->80/tcp   bold_hoover

# 전체 컨테이너 (종료 포함)
$ docker ps -a
# (출력 결과)
CONTAINER ID   IMAGE                    COMMAND                   CREATED         STATUS                     PORTS                                 NAMES
72961c5394a0   hello-world              "/hello"                  2 minutes ago   Exited (0) 2 minutes ago                                         relaxed_lehmann
a17289afee30   hello-world              "/hello"                  3 minutes ago   Exited (0) 3 minutes ago                                         cranky_pasteur
07d062566d06   hello-world              "/hello"                  10 hours ago    Exited (0) 10 hours ago                                          epic_hermann
e43ffadef6ff   docker/getting-started   "/docker-entrypoint.…"   10 hours ago    Up 10 hours                0.0.0.0:80->80/tcp, [::]:80->80/tcp   bold_hoover
```

### 7-4. ubuntu 컨테이너 실행 및 내부 명령 수행

```bash
$ docker run -it ubuntu bash
root@컨테이너ID:/# ls
# (출력 결과)
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

root@컨테이너ID:/# echo "I am inside a container"
# (출력 결과)
I am inside a container

root@컨테이너ID:/# cat /etc/os-release
# (출력 결과)
PRETTY_NAME="Ubuntu 24.04.4 LTS"
NAME="Ubuntu"
VERSION_ID="24.04"
VERSION="24.04.4 LTS (Noble Numbat)"
VERSION_CODENAME=noble
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=noble
LOGO=ubuntu-logo

root@컨테이너ID:/# exit
exit
```

### 7-5. 로그 확인

```bash
$ docker logs (컨테이너 이름 또는 ID)
# (출력 결과)
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/03/30 07:41:02 [notice] 1#1: using the "epoll" event method
2026/03/30 07:41:02 [notice] 1#1: nginx/1.23.3
2026/03/30 07:41:02 [notice] 1#1: built by gcc 12.2.1 20220924 (Alpine 12.2.1_git20220924-r4) 
2026/03/30 07:41:02 [notice] 1#1: OS: Linux 6.17.8-orbstack-00308-g8f9c941121b1
2026/03/30 07:41:02 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 20480:1048576
2026/03/30 07:41:02 [notice] 1#1: start worker processes
2026/03/30 07:41:02 [notice] 1#1: start worker process 30
2026/03/30 07:41:02 [notice] 1#1: start worker process 31
2026/03/30 07:41:02 [notice] 1#1: start worker process 32
2026/03/30 07:41:02 [notice] 1#1: start worker process 33
2026/03/30 07:41:02 [notice] 1#1: start worker process 34
2026/03/30 07:41:02 [notice] 1#1: start worker process 35
2026/03/30 07:41:23 [notice] 1#1: signal 28 (SIGWINCH) received
2026/03/30 07:41:23 [notice] 1#1: signal 28 (SIGWINCH) received
2026/03/30 07:41:23 [notice] 1#1: signal 28 (SIGWINCH) received
2026/03/30 07:41:23 [notice] 1#1: signal 28 (SIGWINCH) received
2026/03/30 07:41:23 [notice] 1#1: signal 28 (SIGWINCH) received
2026/03/30 07:41:24 [notice] 1#1: signal 28 (SIGWINCH) received
2026/03/30 07:57:58 [notice] 1#1: signal 1 (SIGHUP) received, reconfiguring
2026/03/30 07:57:58 [notice] 1#1: reconfiguring
2026/03/30 07:57:58 [notice] 1#1: using the "epoll" event method
2026/03/30 07:57:58 [notice] 1#1: start worker processes
2026/03/30 07:57:58 [notice] 1#1: start worker process 36
2026/03/30 07:57:58 [notice] 1#1: start worker process 37
2026/03/30 07:57:58 [notice] 1#1: start worker process 38
2026/03/30 07:57:58 [notice] 1#1: start worker process 39
2026/03/30 07:57:58 [notice] 1#1: start worker process 40
2026/03/30 07:57:58 [notice] 1#1: start worker process 41
2026/03/30 07:57:58 [notice] 30#30: gracefully shutting down
2026/03/30 07:57:58 [notice] 30#30: exiting
2026/03/30 07:57:58 [notice] 30#30: exit
2026/03/30 07:57:58 [notice] 31#31: gracefully shutting down
2026/03/30 07:57:58 [notice] 31#31: exiting
2026/03/30 07:57:58 [notice] 34#34: gracefully shutting down
2026/03/30 07:57:58 [notice] 34#34: exiting
2026/03/30 07:57:58 [notice] 32#32: gracefully shutting down
2026/03/30 07:57:58 [notice] 33#33: gracefully shutting down
2026/03/30 07:57:58 [notice] 32#32: exiting
2026/03/30 07:57:58 [notice] 33#33: exiting
2026/03/30 07:57:58 [notice] 31#31: exit
2026/03/30 07:57:58 [notice] 35#35: gracefully shutting down
2026/03/30 07:57:58 [notice] 35#35: exiting
2026/03/30 07:57:58 [notice] 35#35: exit
2026/03/30 07:57:58 [notice] 32#32: exit
2026/03/30 07:57:58 [notice] 33#33: exit
2026/03/30 07:57:58 [notice] 34#34: exit
2026/03/30 07:57:58 [notice] 1#1: signal 17 (SIGCHLD) received from 33
2026/03/30 07:57:58 [notice] 1#1: worker process 33 exited with code 0
2026/03/30 07:57:58 [notice] 1#1: signal 29 (SIGIO) received
2026/03/30 07:57:58 [notice] 1#1: signal 17 (SIGCHLD) received from 35
2026/03/30 07:57:58 [notice] 1#1: worker process 35 exited with code 0
2026/03/30 07:57:58 [notice] 1#1: signal 29 (SIGIO) received
2026/03/30 07:57:58 [notice] 1#1: signal 17 (SIGCHLD) received from 32
2026/03/30 07:57:58 [notice] 1#1: worker process 32 exited with code 0
2026/03/30 07:57:58 [notice] 1#1: signal 29 (SIGIO) received
2026/03/30 07:57:58 [notice] 1#1: signal 17 (SIGCHLD) received from 34
2026/03/30 07:57:58 [notice] 1#1: worker process 31 exited with code 0
2026/03/30 07:57:58 [notice] 1#1: worker process 34 exited with code 0
2026/03/30 07:57:58 [notice] 1#1: signal 29 (SIGIO) received
2026/03/30 07:57:58 [notice] 1#1: signal 17 (SIGCHLD) received from 30
2026/03/30 07:57:58 [notice] 1#1: worker process 30 exited with code 0
2026/03/30 07:57:58 [notice] 1#1: signal 29 (SIGIO) received
```

### 7-6. 리소스 확인

```bash
$ docker stats --no-stream
# (출력 결과)
CONTAINER ID   NAME          CPU %     MEM USAGE / LIMIT     MEM %     NET I/O         BLOCK I/O         PIDS
e43ffadef6ff   bold_hoover   0.00%     4.305MiB / 15.67GiB   0.03%     1.91kB / 126B   8.11MB / 8.19kB   7
```

### 7-7. attach와 exec의 차이

```bash
# 백그라운드로 ubuntu 실행
$ docker run -d --name test-ubuntu ubuntu sleep infinity

# exec: 새 프로세스로 진입 → exit해도 컨테이너 유지
$ docker exec -it test-ubuntu bash
root@...:/# echo "exec test"
root@...:/# exit

$ docker ps
# (출력 결과 - 컨테이너가 여전히 실행 중)

# 정리
$ docker rm -f test-ubuntu

# attach 실습을 위해 메인 프로세스가 출력을 내는 컨테이너를 실행
$ docker run -d --init --name attach-test ubuntu bash -c "trap 'echo Goodbye!; exit 0' SIGINT SIGTERM; while true; do echo 'hello from main process'; sleep 2; done"

# attach: 메인 프로세스의 출력에 직접 연결됨
$ docker attach attach-test
hello from main process
hello from main process
hello from main process
# → 메인 프로세스(while 루프)의 출력이 그대로 보임
# → Ctrl+C를 누르면 메인 프로세스가 종료 → 컨테이너도 함께 종료됨

$ docker ps -a | grep attach-test
# (출력 결과 - STATUS가 Exited로 표시됨 → 컨테이너가 종료된 상태)

# 정리
$ docker rm attach-test
```

| 구분 | exec | attach |
|------|------|--------|
| 동작 | 컨테이너에 새 프로세스(셸)를 추가 | 메인 프로세스의 입출력에 직접 연결 |
| exit 시 | 새 프로세스만 종료, 컨테이너 유지 | 메인 프로세스가 종료되면 컨테이너도 종료 |
| 용도 | 디버깅, 내부 확인 (일반적 사용) | 메인 프로세스 출력 확인 |

---

## 8. Dockerfile 커스텀 이미지

### 8-1. 베이스 이미지 선택

**방식 (A):** 웹 서버 베이스 이미지(`nginx:alpine`) + 정적 콘텐츠 교체

### 8-2. 프로젝트 구조

```
workstation/
├── Dockerfile
├── site/
│   └── index.html
├── screenshots/
├── README.md
├── practice/
├── etc...
└── .gitignore
```

### 8-3. 소스코드

**site/index.html:**
```html
<!DOCTYPE html>
<html>
<head><title>Codyssey Workstation</title></head>
<body>
  <h1>Hello, Codyssey!</h1>
  <p>Docker workstation setup complete.</p>
</body>
</html>
```

### 8-4. Dockerfile

```dockerfile
# 베이스 이미지: nginx의 경량 alpine 버전
FROM nginx:alpine

# 이미지 메타데이터
LABEL maintainer="(이름)"
LABEL description="Codyssey workstation mission - custom nginx"

# 환경 변수 설정 (커스텀 포인트 1: 실행 환경 구분)
ENV APP_ENV=development

# 정적 콘텐츠 복사 (커스텀 포인트 2: 기본 페이지를 내 콘텐츠로 교체)
COPY ./site/ /usr/share/nginx/html/

# 컨테이너가 사용하는 포트 선언
EXPOSE 80
```

**커스텀 포인트 요약:**

| 포인트 | 내용 | 목적 |
|--------|------|------|
| ENV APP_ENV | 환경 변수 설정 | 개발/운영 환경을 코드 수정 없이 구분 |
| COPY ./site/ | 정적 콘텐츠 교체 | nginx 기본 페이지 대신 직접 만든 페이지를 서빙 |

### 8-5. 빌드

```bash
$ docker build -t my-web:1.0 .
# (출력 결과)
[+] Building 7.5s (7/7) FINISHED                                docker:orbstack
 => [internal] load build definition from Dockerfile                       0.1s
 => => transferring dockerfile: 517B                                       0.0s
 => [internal] load metadata for docker.io/library/nginx:alpine            2.8s
 => [internal] load .dockerignore                                          0.2s
 => => transferring context: 2B                                            0.0s
 => [internal] load build context                                          0.2s
 => => transferring context: 236B                                          0.0s
 => [1/2] FROM docker.io/library/nginx:alpine@sha256:e7257f1ef28ba17cf7c2  3.5s
 => => resolve docker.io/library/nginx:alpine@sha256:e7257f1ef28ba17cf7c2  0.2s
 => => sha256:7e89aa6cabfc80f566b1b77b981f4bb98413bd2d513 2.50kB / 2.50kB  0.0s
 => => sha256:e7257f1ef28ba17cf7c248cb8ccf6f0c6e0228ab9 10.33kB / 10.33kB  0.0s
 => => sha256:d5030d429039a823bef4164df2fad7a0defb8d00c 12.32kB / 12.32kB  0.0s
 => => sha256:589002ba0eaed121a1dbf42f6648f29e5be55d5c8a6 3.86MB / 3.86MB  0.5s
 => => sha256:8892f80f46a05d59a4cde3bcbb1dd26ed2441d42148 1.87MB / 1.87MB  0.9s
 => => sha256:91d1c9c22f2c631288354fadb2decc448ce151d7a197c16 626B / 626B  0.7s
 => => extracting sha256:589002ba0eaed121a1dbf42f6648f29e5be55d5c8a6ee0f8  0.1s
 => => sha256:cf1159c696ee2a72b85634360dbada071db61bceaad253d 953B / 953B  1.0s
 => => sha256:3f4ad4352d4f91018e2b4910b9db24c08e70192c3b75d0d 402B / 402B  1.2s
 => => extracting sha256:8892f80f46a05d59a4cde3bcbb1dd26ed2441d4214870a4a  0.1s
 => => sha256:c2bd5ab177271dd59f19a46c214b1327f5c428cd075 1.21kB / 1.21kB  1.4s
 => => extracting sha256:91d1c9c22f2c631288354fadb2decc448ce151d7a197c167  0.0s
 => => sha256:4d9d41f3822d171ccc5f2cdfd75ad846ac4c7ed1cd3 1.40kB / 1.40kB  1.5s
 => => extracting sha256:cf1159c696ee2a72b85634360dbada071db61bceaad253db  0.0s
 => => extracting sha256:3f4ad4352d4f91018e2b4910b9db24c08e70192c3b75d0d6  0.0s
 => => sha256:3370263bc02adcf5c4f51831d2bf1d54dbf9a6a80 20.25MB / 20.25MB  1.9s
 => => extracting sha256:c2bd5ab177271dd59f19a46c214b1327f5c428cd075437ec  0.0s
 => => extracting sha256:4d9d41f3822d171ccc5f2cdfd75ad846ac4c7ed1cd36fb99  0.0s
 => => extracting sha256:3370263bc02adcf5c4f51831d2bf1d54dbf9a6a80b0bf32c  0.4s
 => [2/2] COPY ./site/ /usr/share/nginx/html/                              0.3s
 => exporting to image                                                     0.2s
 => => exporting layers                                                    0.1s
 => => writing image sha256:f611d3f8c7d33c9f2f57490f477b865fbba4e8543906a  0.0s
 => => naming to docker.io/library/my-web:1.0                              0.0s

$ docker images | grep my-web
# (출력 결과)
my-web                   1.0       f611d3f8c7d3   15 seconds ago   62.2MB
```

---

## 9. 포트 매핑 접속 증거

### 9-1. 컨테이너 실행 (포트 2개)

```bash
# 첫 번째: 호스트 8080 → 컨테이너 80
$ docker run -d -p 8080:80 --name my-web-8080 my-web:1.0
# (출력 결과)
0f1deb9a73575a201b20c1ffa2434aeec6673ca0346f8798962ffe6a412e5eaa

# 두 번째: 호스트 8081 → 컨테이너 80
$ docker run -d -p 8081:80 --name my-web-8081 my-web:1.0
# (출력 결과)
23153aa972f64550deaff0861b8cb81cbe582bdd978b81ede9b7f23d81f92b85

```

### 9-2. 접속 확인

```bash
$ curl http://localhost:8080
# (출력 결과)
<!DOCTYPE html>
<html>
<head><title>Codyssey Workstation</title></head>
<body>
	<h1>Hello, Codyssey</h1>
	<p>Docker workstation setup complete.</p>
</body>
</html>

$ curl http://localhost:8081
# (출력 결과)
<!DOCTYPE html>
<html>
<head><title>Codyssey Workstation</title></head>
<body>
	<h1>Hello, Codyssey</h1>
	<p>Docker workstation setup complete.</p>
</body>
</html>


$ docker ps
# (출력 결과 - 두 컨테이너가 모두 실행 중)
CONTAINER ID   IMAGE                    COMMAND                   CREATED          STATUS          PORTS                                     NAMES
23153aa972f6   my-web:1.0               "/docker-entrypoint.…"   41 seconds ago   Up 41 seconds   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   my-web-8081
0f1deb9a7357   my-web:1.0               "/docker-entrypoint.…"   46 seconds ago   Up 45 seconds   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   my-web-8080
e43ffadef6ff   docker/getting-started   "/docker-entrypoint.…"   12 hours ago     Up 12 hours     0.0.0.0:80->80/tcp, [::]:80->80/tcp       bold_hoover
```

### 9-3. 브라우저 접속 스크린샷

| 포트 | 스크린샷 |
|------|----------|
| localhost:8080 | ![8080 접속](./screenshots/port-8080.png) |
| localhost:8081 | ![8081 접속](./screenshots/port-8081.png) |

> 주소창에 포트 번호가 포함되어 있고, 커스텀 페이지("Hello, Codyssey!")가 정상 표시된다.

---

## 10. 바인드 마운트 반영 증거

### 10-1. 바인드 마운트로 컨테이너 실행

```bash
# 기존 컨테이너 정리
$ docker rm -f my-web-8080 my-web-8081

# 바인드 마운트로 실행
$ docker run -d -p 8080:80 \
  --name my-web-bind \
  -v $(pwd)/site:/usr/share/nginx/html \
  nginx:alpine
```

### 10-2. 변경 전 확인

```bash
$ curl http://localhost:8080
# (출력 결과 - "Hello, Codyssey!" 원본 내용)
```

### 10-3. 호스트에서 파일 수정

```bash
$ cat > site/index.html << 'EOF'
<!DOCTYPE html>
<html>
<head><title>Updated!</title></head>
<body>
  <h1>Bind Mount Works!</h1>
  <p>This content was changed on the HOST machine.</p>
</body>
</html>
EOF
```

### 10-4. 변경 후 확인 (컨테이너 재시작 없이)

```bash
$ curl http://localhost:8080
# (출력 결과 - "Bind Mount Works!" 변경된 내용)
<!DOCTYPE html>
<html>
<head><title>Updated!</title></head>
<body>
  <h1>Bind Mount Works!</h1>
  <p>This content was changed on the HOST machine.</p>
</body>
</html>
EOF
codewhite77770105@c4r10s1 workstation % curl http://localhost:8080
<!DOCTYPE html>
<html>
<head><title>Updated!</title></head>
<body>
  <h1>Bind Mount Works!</h1>
  <p>This content was changed on the HOST machine.</p>
</body>
</html>
```

### 10-5. 전후 비교

| 시점 | 내용 |
|------|------|
| 변경 전 | "Hello, Codyssey!" |
| 호스트에서 index.html 수정 | |
| 변경 후 | "Bind Mount Works!" |

> **핵심:** 컨테이너를 재시작하지 않았는데도, 호스트에서 파일을 수정하자 즉시 반영되었다. 바인드 마운트는 호스트와 컨테이너의 폴더를 실시간으로 연결하기 때문이다.

```bash
# 정리
$ docker rm -f my-web-bind
```

---

## 11. Docker 볼륨 영속성 증거

### 11-1. 볼륨 생성 및 데이터 쓰기

```bash
# 볼륨 생성
$ docker volume create mydata

# 컨테이너에 볼륨 연결 후 실행
$ docker run -d --name vol-test \
  -v mydata:/data \
  ubuntu sleep infinity

# 데이터 쓰기
$ docker exec vol-test bash -c "echo 'persistent data!' > /data/hello.txt"

# 데이터 확인
$ docker exec vol-test bash -c "cat /data/hello.txt"
# (출력 결과 - "persistent data!")
```

### 11-2. 컨테이너 삭제

```bash
$ docker rm -f vol-test

# 삭제 확인
$ docker ps -a | grep vol-test
# (출력 결과 - 아무것도 나오지 않음)
```

### 11-3. 새 컨테이너에서 데이터 확인

```bash
# 새 컨테이너에 같은 볼륨 연결
$ docker run -d --name vol-test-2 \
  -v mydata:/data \
  ubuntu sleep infinity

# 데이터 확인
$ docker exec vol-test-2 bash -c "cat /data/hello.txt"
# (출력 결과 - "persistent data!" 여전히 존재!)
```

### 11-4. 전후 비교

| 단계 | 명령 | 결과 |
|------|------|------|
| 1. 데이터 쓰기 | `echo 'persistent data!' > /data/hello.txt` | 파일 생성됨 |
| 2. 컨테이너 삭제 | `docker rm -f vol-test` | 컨테이너 제거됨 |
| 3. 새 컨테이너에서 확인 | `cat /data/hello.txt` | **"persistent data!" 유지됨** |

> **핵심:** 컨테이너를 삭제해도 Docker 볼륨에 저장된 데이터는 사라지지 않는다. 볼륨은 컨테이너의 생명주기와 독립적으로 관리되기 때문이다.

```bash
# 정리
$ docker rm -f vol-test-2
$ docker volume rm mydata
```

---

## 12. Git 설정 및 GitHub 연동

### 12-1. Git 사용자 설정

```bash
$ git config --global user.name "(이름)"
$ git config --global user.email "(이메일)"
$ git config --global init.defaultBranch main
```

### 12-2. 설정 확인

```bash
$ git config --list
# (출력 결과 - user.name, user.email, init.defaultBranch 확인)
# ⚠️ credential 관련 항목은 마스킹 처리
```

### 12-3. 저장소 초기화 및 GitHub 연동

```bash
$ cd ~/codyssey/workstation
$ git init
$ git add .
$ git commit -m "feat: initial workstation setup"
$ git remote add origin https://github.com/(아이디)/(저장소이름).git
$ git push -u origin main
```

### 12-4. VSCode GitHub 연동 증거

![VSCode GitHub 연동](./screenshots/vscode-github.png)

> VSCode 좌측 하단에 GitHub 계정이 연결되어 있고, Source Control 탭에서 저장소가 정상 인식되는 것을 확인할 수 있다.

---

## 13. 트러블슈팅

### 문제 1: docker attach 후 Ctrl+C로 컨테이너가 종료되지 않는 문제

- **현상:** `docker attach`로 컨테이너에 접속한 뒤 `Ctrl+C`를 눌렀지만,
컨테이너가 종료되지 않고 계속 실행되었다.
```bash
$ docker run -d --name attach-test ubuntu bash -c "while true; do echo 'hello from main process'; sleep 2; done"

$ docker attach attach-test
hello from main process
hello from main process
^C
hello from main process   # ← Ctrl+C를 눌렀는데 또 출력됨
^C
hello from main process
^C
got 3 SIGTERM/SIGINTs, forcefully exiting   # ← 3번째에서 attach만 빠져나옴

#Ctrl+C를 3번 누른 뒤 터미널은 호스트로 돌아왔지만,
#컨테이너를 확인하면 여전히 실행 중(Up) 상태였다.

$ docker ps | grep attach-test
46883ea3684b   ubuntu   "bash -c 'while true…"   Up 4 minutes   attach-test
```
- **원인 가설:** `while true` 루프 안의 `sleep`만 죽고, 루프 자체는 계속 반복되는 것이 아닌가 추측했다.
- **확인 방법:** Ctrl+C가 보내는 SIGINT 신호가 실제로 어디에 전달되는지 추적했다.

1번째 Ctrl+C
├── SIGINT 신호가 전달됨
├── 현재 실행 중인 프로세스는 sleep 2
├── sleep은 SIGINT를 받으면 즉시 종료됨 ← sleep만 죽음
├── bash의 while true 루프로 제어가 돌아감
├── "true"는 항상 참이므로 다음 반복 시작
└── echo 'hello' 실행 → 새로운 sleep 2 시작 → 다시 대기

2번째 Ctrl+C
├── 또다시 sleep만 죽음
├── while true → 다음 반복 시작
└── 컨테이너는 계속 실행됨

3번째 Ctrl+C
├── Docker attach 클라이언트가 "3번 시그널을 보냈지만 프로세스가 종료되지 않음"으로 판단
└── attach 클라이언트 자신이 연결을 끊음 (강제 detach)
    → 컨테이너 내부의 while true 루프는 영향 없이 계속 실행 중

- **해결/대안:** `--init` 옵션으로 시그널을 적절히 처리함
**--init이 해결하는 방식:**

`--init` 옵션을 추가하면 Docker가 `tini`라는 초경량 init 프로세스를
PID 1로 먼저 실행하고, 실제 명령은 그 아래 자식 프로세스로 실행한다.
```
--init 없이:
┌─────────────────────────────────┐
│ PID 1: bash -c "while true..." │ ← 시그널 무시, 좀비 미수거
└─────────────────────────────────┘

--init 사용:
┌─────────────────────────────────┐
│ PID 1: tini (init 프로세스)     │ ← 시그널을 받아서 자식에게 전달
│  └── PID 2: bash -c "while..." │ ← PID 1이 아니라서 시그널에 정상 반응
└─────────────────────────────────┘
```

**tini가 하는 일:**
```
1. 시그널 전달
   Ctrl+C (SIGINT) → tini가 수신 → 자식 프로세스(bash)에게 전달
   → bash는 PID 1이 아니므로 정상적으로 시그널 처리

2. 좀비 프로세스 수거
   자식 프로세스가 종료되면 tini가 wait()를 호출하여 정리
   → 좀비 프로세스가 쌓이지 않음

3. 종료 전파
   자식 프로세스가 종료되면 tini도 같은 종료 코드로 종료
   → 컨테이너가 정상적으로 Exited 상태가 됨
```

### 문제 2: _(제목)_

- **현상:** _(어떤 명령을 실행했을 때 어떤 에러/증상이 발생했는지)_
- **원인 가설:** _(왜 이런 문제가 생겼을 것이라고 추측했는지)_
- **확인 방법:** _(가설을 검증하기 위해 어떤 명령/조치를 취했는지)_
- **해결/대안:** _(최종적으로 어떻게 해결했는지)_

---

## 14. 검증 방법 요약

| 검증 항목 | 확인 명령 / 방법 | 결과 위치 |
|-----------|------------------|-----------|
| 터미널 조작 | pwd, ls, mkdir, cp, mv, rm, cat, touch | [4. 터미널 조작 로그](#4-터미널-조작-로그) |
| 권한 변경 | chmod + ls -la 전후 비교 | [5. 권한 실습](#5-권한-실습) |
| Docker 설치 | docker --version, docker info | [6. Docker 설치 및 기본 점검](#6-docker-설치-및-기본-점검) |
| Docker 운영 | docker images, ps, logs, stats | [7. Docker 기본 운영 명령](#7-docker-기본-운영-명령) |
| 커스텀 이미지 | docker build + docker images | [8. Dockerfile 커스텀 이미지](#8-dockerfile-커스텀-이미지) |
| 포트 매핑 | curl + 브라우저 스크린샷 | [9. 포트 매핑 접속 증거](#9-포트-매핑-접속-증거) |
| 바인드 마운트 | 호스트 파일 수정 → curl 전후 비교 | [10. 바인드 마운트 반영 증거](#10-바인드-마운트-반영-증거) |
| 볼륨 영속성 | 컨테이너 삭제 → 재연결 → cat | [11. Docker 볼륨 영속성 증거](#11-docker-볼륨-영속성-증거) |
| Git/GitHub | git config --list + VSCode 스크린샷 | [12. Git 설정 및 GitHub 연동](#12-git-설정-및-github-연동) |
