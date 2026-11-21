# E1-1. Dev Setup

## 1) Environment

| 항목 | 버전 |
| --- | --- |
| OS | Ubuntu 20.04.6 LTS |
| Shell | Bash 5.0.17(1)-release |
| Docker | 23.0.3 |
| Git | 2.25.1 |

## 2) Checklist

- [X] (1) Terminal & Folder - Basic terminal operations and folder setup
- [X] (2) Permission - Practice changing file permissions
- [X] (3) Docker Installation - Install and verify Docker
- [X] (4) Docker Build / Execution - Build and run a Docker image using a Dockerfile
- [ ] (5) "hello-world" Execution - 전용 이미지의 실행 로그와 화면 캡처 수집 필요
- [X] (6) Port Mapping & Access - Access containers through port mapping (2 times)
- [ ] (7) Bind Mount - 호스트 ↔ 컨테이너 양방향 변경 결과 수집 필요
- [X] (8) Volume - Verify Docker volume persistence
- [X] (9) Git & VSCode Github - Configure Git and connect VS Code to GitHub

### 저장소 구조

```text
Dev_Setup/
├── README.md                    실습 절차, 실행 기록, 결과 판독 기준
├── images/                      Docker 실습에서 촬영한 화면
│   ├── docker-images.png        빌드한 이미지의 태그·ID·크기
│   ├── docker-containers.png    실행 중인 웹 컨테이너와 포트 매핑
│   ├── docker-web.png           브라우저 접속 결과 (8080)
│   ├── docker-web2.png          브라우저 접속 결과 (8081)
│   └── docker-stats.png         컨테이너 자원 사용량
└── evidence/
    ├── terminal-session.txt     파일·권한 실습과 Git 진단을 재실행한 Bash 원본 로그
    └── host-checks.txt          호스트 TCP 포트 충돌과 파일 백업·복구 원본 로그
```

화면 캡처는 `images/`, 명령과 출력을 다시 대조할 수 있는 텍스트는 `evidence/`에 둔다. Docker 빌드 입력인 `Dockerfile`과 `site/index.html`은 아래 재현 명령이 별도의 임시 작업 디렉토리에 생성한다.

## 3) Details

- [0. Version Check](#step-0)
- [1. Terminal & Folder](#step-1)
- [2. Permission](#step-2)
- [3. Docker Installation](#step-3)
- [4. Docker Build / Execution](#step-4)
- [5. "hello-world" Execution](#step-5)
- [6. Port Mapping & Access](#step-6)
- [7. Bind Mount](#step-7)
- [8. Volume](#step-8)
- [9. Git & VSCode Github](#step-9)
- [Docker 실습 재현 및 증거 수집](#docker-replay)
- [트러블슈팅 — 포트 충돌 및 Git 작업 위치](#troubleshooting)

<a id="step-0"></a>

### (0) Version Check

- Ubuntu Terminal에서 아래 Bash Command로 환경 정보를 확인

| 항목 | 명령어 | 출력 내용 | 출력 예시 |
| --- | --- | --- | --- |
| OS | `cat /etc/os-release` | 운영체제 정보 (`PRETTY_NAME`에서 Ubuntu 버전 확인) | `PRETTY_NAME="Ubuntu 20.04.6 LTS"` |
| 현재 Shell | `ps -p $$ -o comm=` | 현재 실행 중인 Shell 이름 | `bash` |
| Bash 버전 | `echo "$BASH_VERSION"` | 현재 Bash의 버전 문자열 | `5.0.17(1)-release` |
| Docker CLI 버전 | `docker --version` | Docker 클라이언트 버전과 빌드 정보 | `Docker version 23.0.3, build 3e7cbfd` |
| Git 버전 | `git --version` | 설치된 Git 버전 | `git version 2.25.1` |

#### OS - Check Ubuntu Version

- **설명**

    - `cat /etc/os-release`는 운영체제 정보 파일의 내용을 출력
    - `PRETTY_NAME`에서 Ubuntu 버전 확인
    - `ID_LIKE`는 현재 배포판과 계열이 비슷하거나 기반이 되는 배포판의 ID를 나타냄

- **명령어**

    ```bash
    cat /etc/os-release
    ```

- **실행 결과**

    ```text
    NAME="Ubuntu"
    VERSION="20.04.6 LTS (Focal Fossa)"
    ID=ubuntu
    ID_LIKE=debian
    PRETTY_NAME="Ubuntu 20.04.6 LTS"
    VERSION_ID="20.04"
    HOME_URL="https://www.ubuntu.com/"
    SUPPORT_URL="https://help.ubuntu.com/"
    BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
    PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
    VERSION_CODENAME=focal
    UBUNTU_CODENAME=focal
    ```

#### Shell - Check Current Shell

- **설명**

    - `ps -p $$ -o comm=`는 현재 셸 프로세스의 명령 이름 출력
    - `$$`는 현재 셸의 프로세스 ID, `-p`는 조회할 프로세스 선택, `-o comm=`는 열 제목 없이 명령 이름만 출력하는 옵션
    - `bash`가 출력되면 현재 셸이 Bash임을 의미
    - `Bash(Bourne Again SHell)` : UNIX 계열 OS를 위해 개발된 상호작용형 CLI(Command Line Interface)이자 Script Language 

- **명령어**

    ```bash
    ps -p $$ -o comm=
    ```

- **실행 결과**

    ```text
    bash
    ```

#### Bash - Check Bash Version

- **설명**

    - `echo "$BASH_VERSION"`은 현재 Bash의 버전 문자열을 출력
    - `5.0.17(1)-release`는 **Bash의 버전**이므로 환경 정보에는 `Bash 5.0.17(1)-release`처럼 셸 이름과 함께 작성
    - Bash 이외의 셸에서는 이 변수가 설정되지 않아 빈 값이 출력될 수 있음

- **명령어**

    ```bash
    echo "$BASH_VERSION"
    ```

- **실행 결과**

    ```text
    5.0.17(1)-release
    ```

#### Docker - Check CLI Version

- **설명**

    - `docker --version`은 설치된 **Docker CLI(클라이언트)의 버전과 빌드 정보**를 출력
    - 아래 결과에서 버전은 `23.0.3`, 빌드는 `3e7cbfd`

- **명령어**

    ```bash
    docker --version
    ```

- **실행 결과**

    ```text
    Docker version 23.0.3, build 3e7cbfd
    ```

#### Docker - Check Engine Connection

- **설명**

    - `docker version`은 Docker 클라이언트와 서버(엔진)의 버전 정보를 확인
    - 엔진이 실행 중이고 현재 CLI에서 정상적으로 연결되면 `Client`와 `Server` 정보가 모두 출력됨
        - `Client`: Docker CLI의 버전 및 관련 정보
        - `Server`: 연결된 Docker 엔진의 버전 및 관련 정보
    - 엔진에 연결할 수 없으면 서버 정보를 얻지 못하고 연결 오류가 표시될 수 있음

- **명령어**

    ```bash
    docker version
    ```

- **실행 결과**

    ```bash
    Client: Docker Engine - Community
     Version:           23.0.3
     API version:       1.42
     Go version:        go1.19.7
     Git commit:        3e7cbfd
     Built:             Tue Apr  4 22:06:10 2023
     OS/Arch:           linux/amd64
     Context:           default

    Server: Docker Engine - Community
     Engine:
      Version:          23.0.3
      API version:      1.42 (minimum version 1.12)
      Go version:       go1.19.7
      Git commit:       59118bf
      Built:            Tue Apr  4 22:06:10 2023
      OS/Arch:          linux/amd64
      Experimental:     false
     containerd:
      Version:          1.6.20
      GitCommit:        2806fc1057397dbaeefbea0e4e17bddfbd388f38
     runc:
      Version:          1.1.5
      GitCommit:        v1.1.5-0-gf19387a
     docker-init:
      Version:          0.19.0
      GitCommit:        de40ad0
    ```

#### Git - Check Git Version

- **설명**

    - `git --version`은 설치된 Git의 버전을 출력
    - 아래 결과의 `2.25.1`이 Git 버전

- **명령어**

    ```bash
    git --version
    ```

- **실행 결과**

    ```text
    git version 2.25.1
    ```

<a id="step-1"></a>

### (1) Terminal & Folder - Basic terminal operations and folder setup

2026-11-21에 임시 디렉토리에서 파일 생성, 내용 확인, 복사, 이동, 이름 변경과 `chmod`를 다시 실행했다. 아래 기존 기록과 별도로 재실행한 세션의 명령과 출력을 순서대로 보관했다. [Bash 세션 원본 로그](./evidence/terminal-session.txt)

<details>
<summary>파일 권한·Git·Docker 환경 확인 로그 전체 보기</summary>

```text
# 터미널과 권한 실습 재현
date -Is
2026-11-21T19:19:31+09:00
pwd
/tmp/dev-setup-terminal-v5vx3odp
umask 022
touch hello_world.txt
ls -la
total 220
drwx------ 2 root root   4096 Nov 21 19:19 .
drwxrwxrwt 1 root root 217088 Nov 21 19:19 ..
-rw-r--r-- 1 root root      0 Nov 21 19:19 hello_world.txt
printf 'Hello World!\n' > hello_world.txt
cat hello_world.txt
Hello World!
mkdir dev_setup
cd dev_setup
pwd
/tmp/dev-setup-terminal-v5vx3odp/dev_setup
cd ..
cp hello_world.txt hello_world_cp.txt
mv hello_world_cp.txt dev_setup/
cd dev_setup
mv hello_world_cp.txt renamed_hello_world.txt
ls -l renamed_hello_world.txt
-rw-r--r-- 1 root root 13 Nov 21 19:19 renamed_hello_world.txt
chmod 755 renamed_hello_world.txt
ls -l renamed_hello_world.txt
-rwxr-xr-x 1 root root 13 Nov 21 19:19 renamed_hello_world.txt
cd ..
chmod 644 dev_setup
ls -ld dev_setup
drw-r--r-- 2 root root 4096 Nov 21 19:19 dev_setup
chmod 755 dev_setup
ls -ld dev_setup
drwxr-xr-x 2 root root 4096 Nov 21 19:19 dev_setup

# Git 작업 위치 오류와 복구, 현재 저장소의 push 기록
cd /root
pwd
/root
git rev-parse --show-toplevel || true
fatal: not a git repository (or any parent up to mount point /)
Stopping at filesystem boundary (GIT_DISCOVERY_ACROSS_FILESYSTEM not set).
cd /root/Dev_Setup
git rev-parse --show-toplevel
/root/Dev_Setup
git remote get-url origin
https://github.com/eunhye0323/Dev_Setup.git
git branch -vv
* master 7a2e4f3 [origin/master] update dev setup
git log -1 --date=iso-local --format='%h %cd %s'
7a2e4f3 2026-11-21 19:04:12 +0900 update dev setup
git reflog show -1 --date=iso-local --format='%h %gd %gs' refs/remotes/origin/master
7a2e4f3 origin/master@{2026-11-21 19:04:18 +0900} update by push
git rev-parse HEAD
7a2e4f34cc2d68774b5fca1a22e65bc994d1761f
git ls-remote origin refs/heads/master
7a2e4f34cc2d68774b5fca1a22e65bc994d1761f	refs/heads/master

# 편집 환경에서 Docker 실행 가능 여부 확인
ps -p 1 -o comm=
bash
docker version || true
bash: line 41: docker: command not found
ls -l /var/run/docker.sock || true
ls: cannot access '/var/run/docker.sock': No such file or directory
systemctl is-active docker.service || true
System has not been booted with systemd as init system (PID 1). Can't operate.
Failed to connect to bus: Host is down
```

</details>

#### pwd - Check Current Directory

- **설명**

    - 현재 작업 중인 디렉토리의 절대경로를 출력하는 명령어

- **실행 기록**

    ```bash
    $ pwd
    /root/Dev_Setup
    ```

#### touch - Create Empty File

- **설명**

    - 내용이 없는 빈 파일을 생성하고, `ls -la`로 파일이 정상적으로 만들어졌는지 확인

- **실행 기록**

    ```bash
    $ touch hello_world.txt
    $ ls -la
    total 36
    drwxr-xr-x  4 root root  4096 Nov  9 06:35 .
    drwxrwxr-x 64 1001 1001 12288 Nov  9 06:33 ..
    drwxr-xr-x  8 root root  4096 Nov  9 06:33 .git
    -rw-r--r--  1 root root 10985 Nov  9 06:28 README.md
    -rw-r--r--  1 root root     0 Nov  9 06:35 hello_world.txt
    drwxr-xr-x  2 root root  4096 Nov  9 06:28 images
    ```

#### nano - Write and Verify File Content

- **설명**

    - 터미널 에디터 nano로 텍스트를 작성/저장하고, `cat`으로 저장된 내용을 확인

- **실행 기록**

    ```bash
    $ nano hello_world.txt
    $ cat hello_world.txt
    Hello World!
    ```

#### mkdir - Create Directory

- **설명**

    - 새 디렉토리를 생성하고, `ls -la`로 폴더가 추가되었는지 확인

- **실행 기록**

    ```bash
    $ mkdir dev_setup
    $ ls -la
    total 44
    drwxr-xr-x  5 root root  4096 Nov  9 06:35 .
    drwxrwxr-x 64 1001 1001 12288 Nov  9 06:33 ..
    drwxr-xr-x  8 root root  4096 Nov  9 06:33 .git
    -rw-r--r--  1 root root 10985 Nov  9 06:28 README.md
    drwxr-xr-x  2 root root  4096 Nov  9 06:35 dev_setup
    -rw-r--r--  1 root root    13 Nov  9 06:35 hello_world.txt
    drwxr-xr-x  2 root root  4096 Nov  9 06:28 images
    ```

#### cd - Change Directory

- **설명**

    - 생성한 디렉토리로 이동하고, `pwd`로 현재 경로가 바뀌었는지 확인

- **실행 기록**

    ```bash
    $ cd dev_setup
    $ pwd
    /root/dev_setup/dev_setup
    ```

#### cp - Copy File

- **설명**

    - 기존 파일을 복사해 새 파일을 만들고, `ls -la`로 원본과 복사본이 함께 존재하는지 확인

- **실행 기록**

    ```bash
    $ cd ..
    $ cp hello_world.txt hello_world_cp.txt
    $ ls -la
    total 48
    drwxr-xr-x  5 root root  4096 Nov  9 06:35 .
    drwxrwxr-x 64 1001 1001 12288 Nov  9 06:33 ..
    drwxr-xr-x  8 root root  4096 Nov  9 06:33 .git
    -rw-r--r--  1 root root 10985 Nov  9 06:28 README.md
    drwxr-xr-x  2 root root  4096 Nov  9 06:35 dev_setup
    -rw-r--r--  1 root root    13 Nov  9 06:35 hello_world.txt
    -rw-r--r--  1 root root    13 Nov  9 06:35 hello_world_cp.txt
    drwxr-xr-x  2 root root  4096 Nov  9 06:28 images
    ```

#### mv - Move File

- **설명**

    - `mv`로 파일을 다른 디렉토리로 옮기고, 이동한 위치에서 `ls -la`로 확인

- **실행 기록**

    ```bash
    $ mv ./hello_world_cp.txt ./dev_setup/hello_world_cp.txt
    $ cd dev_setup
    $ ls -la
    total 12
    drwxr-xr-x 2 root root 4096 Nov  9 06:35 .
    drwxr-xr-x 5 root root 4096 Nov  9 06:35 ..
    -rw-r--r-- 1 root root   13 Nov  9 06:35 hello_world_cp.txt
    ```

#### mv - Rename File

- **설명**

    - 같은 디렉토리 내에서 `mv`를 사용하면 이동이 아닌 이름 변경으로 동작함을 확인

- **실행 기록**

    ```bash
    $ mv hello_world_cp.txt renamed_hello_world.txt
    $ ls -la
    total 12
    drwxr-xr-x 2 root root 4096 Nov  9 06:35 .
    drwxr-xr-x 5 root root 4096 Nov  9 06:35 ..
    -rw-r--r-- 1 root root   13 Nov  9 06:35 renamed_hello_world.txt
    ```

<a id="step-2"></a>

### (2) Permission - Practice changing file permissions

#### chmod - Change File Permission

| 숫자 | 계산 | 표기 | 뜻 |
| --- | --- | --- | --- |
| 0 | 0 | `---` | 아무 권한 없음 |
| 1 | 1 | `--x` | 실행만 |
| 2 | 2 | `-w-` | 쓰기만 |
| 3 | 2+1 | `-wx` | 쓰기+실행 |
| 4 | 4 | `r--` | 읽기만 |
| 5 | 4+1 | `r-x` | 읽기+실행 |
| 6 | 4+2 | `rw-` | 읽기+쓰기 |
| 7 | 4+2+1 | `rwx` | 전부 |

**권한이 적용되는 대상**

`chmod`의 세 자리 숫자는 왼쪽부터 User, Group, Others의 권한을 나타냄

| 대상 | 기호 | 권한이 적용되는 사용자 |
| --- | --- | --- |
| User (소유자) | `u` | 파일이나 디렉토리의 소유자 |
| Group (그룹) | `g` | 소유자를 제외한 사용자 중 파일이나 디렉토리의 소유 그룹에 속한 사용자 |
| Others (기타 사용자) | `o` | 소유자도 아니고 소유 그룹에도 속하지 않은 사용자 |

예를 들어 `chmod 755 renamed_hello_world.txt`를 실행하면 소유자는 파일을 읽고 쓰고 실행할 수 있고(`7` = `rwx`), 그룹과 기타 사용자는 각각 읽고 실행할 수 있음(`5` = `r-x`). `ls -l`에 표시되는 `-rwxr-xr-x`에서 맨 앞 `-`는 일반 파일을 뜻하고, 뒤의 아홉 글자는 `rwx`, `r-x`, `r-x` 세 묶음으로 읽음

- **설명**

    - `chmod 755`로 파일에 실행 권한을 부여하고, 변경 전후 `ls -la` 권한 필드를 비교
    - 이 실습에서 `755`를 선택한 이유는 소유자만 파일을 수정할 수 있게 하면서 세 대상 모두에 실행 비트가 생기는 모습을 확인하기 위해서다. `renamed_hello_world.txt`는 텍스트 파일이므로 내용을 읽는 데 실행 권한은 필요하지 않다.

- **실행 기록**

    ```bash
    $ ls -la
    total 12
    drwxr-xr-x 2 root root 4096 Nov  9 06:35 .
    drwxr-xr-x 5 root root 4096 Nov  9 06:35 ..
    -rw-r--r-- 1 root root   13 Nov  9 06:35 renamed_hello_world.txt
    $ chmod 755 renamed_hello_world.txt
    $ ls -la
    total 12
    drwxr-xr-x 2 root root 4096 Nov  9 06:35 .
    drwxr-xr-x 5 root root 4096 Nov  9 06:35 ..
    -rwxr-xr-x 1 root root   13 Nov  9 06:35 renamed_hello_world.txt
    ```

각 자리에서 `r=4`, `w=2`, `x=1`을 더한다. 예를 들어 다른 사용자가 읽어도 되는 자료라면 용도에 따라 다음처럼 선택한다.

| 대상 | 권한 예시 | 선택 이유 |
| --- | --- | --- |
| 일반 문서 | `644` (`rw-r--r--`) | 소유자가 편집하고 다른 사용자는 읽는다. |
| 공유할 디렉토리 | `755` (`rwxr-xr-x`) | 다른 사용자도 목록을 보고(`r`) 경로를 통과할 수 있다(`x`). 항목을 만들거나 삭제하는 권한은 소유자에게 둔다. |
| 직접 실행할 스크립트 | `755` (`rwxr-xr-x`) | 다른 사용자도 스크립트를 읽고 실행할 수 있다. 소유자만 실행할 스크립트에는 `700`을 쓴다. |

#### chmod - Change Directory Permission

- **설명**

    - `chmod 644`는 디렉토리에서 탐색 권한(`x`)을 제거한다. 일반 사용자가 그 디렉토리로 이동하거나 하위 파일을 경로로 열려면 `x`가 필요하다. 아래 root 세션에서 확인한 값은 `ls -la`의 권한 비트다.

- **실행 기록**

    ```bash
    $ cd ..
    $ ls -la
    total 44
    drwxr-xr-x  5 root root  4096 Nov  9 06:35 .
    drwxrwxr-x 64 1001 1001 12288 Nov  9 06:33 ..
    drwxr-xr-x  8 root root  4096 Nov  9 06:33 .git
    -rw-r--r--  1 root root 10985 Nov  9 06:28 README.md
    drwxr-xr-x  2 root root  4096 Nov  9 06:35 dev_setup
    -rw-r--r--  1 root root    13 Nov  9 06:35 hello_world.txt
    drwxr-xr-x  2 root root  4096 Nov  9 06:28 images
    $ chmod 644 dev_setup
    $ ls -la
    total 44
    drwxr-xr-x  5 root root  4096 Nov  9 06:35 .
    drwxrwxr-x 64 1001 1001 12288 Nov  9 06:33 ..
    drwxr-xr-x  8 root root  4096 Nov  9 06:33 .git
    -rw-r--r--  1 root root 10985 Nov  9 06:28 README.md
    drw-r--r--  2 root root  4096 Nov  9 06:35 dev_setup
    -rw-r--r--  1 root root    13 Nov  9 06:35 hello_world.txt
    drwxr-xr-x  2 root root  4096 Nov  9 06:28 images
    ```

<a id="step-3"></a>

### (3) Docker Installation - Install and verify Docker

#### docker --version - Verify Installation

- **설명**

    - 설치된 Docker 클라이언트 버전을 출력해 정상 설치 여부를 확인

- **실행 기록**

    ```bash
    $ docker --version
    Docker version 23.0.3, build 3e7cbfd
    ```

#### docker info - Check Detailed Info

- **설명**

    - Docker 데몬의 상세 설정(스토리지 드라이버, 실행 중인 컨테이너/이미지 수, 런타임 등)을 확인

- **실행 기록**

    ```bash
    $ docker info
    Client:
     Context:    default
     Debug Mode: false
     Plugins:
      buildx: Docker Buildx (Docker Inc.)
        Version:  v0.10.4
        Path:     /usr/libexec/docker/cli-plugins/docker-buildx
      compose: Docker Compose (Docker Inc.)
        Version:  v2.17.2
        Path:     /usr/libexec/docker/cli-plugins/docker-compose

    Server:
     Containers: 73
      Running: 2
      Paused: 0
      Stopped: 71
     Images: 120
     Server Version: 23.0.3
     Storage Driver: overlay2
      Backing Filesystem: extfs
      Supports d_type: true
      Using metacopy: false
      Native Overlay Diff: true
      userxattr: false
     Logging Driver: json-file
     Cgroup Driver: cgroupfs
     Cgroup Version: 1
     Plugins:
      Volume: local
      Network: bridge host ipvlan macvlan null overlay
      Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
     Swarm: inactive
     Runtimes: io.containerd.runc.v2 nvidia runc
     Default Runtime: runc
     Init Binary: docker-init
     containerd version: 2806fc1057397dbaeefbea0e4e17bddfbd388f38
     runc version: v1.1.5-0-gf19387a
     init version: de40ad0
     Security Options:
      apparmor
      seccomp
       Profile: builtin
     Kernel Version: 5.15.0-139-generic
     Operating System: Ubuntu 20.04.6 LTS
     OSType: linux
     Architecture: x86_64
     CPUs: 64
     Total Memory: 251.5GiB
     Name: deepcgv-mk3
     ID: XO3U:27VL:HJ4B:2DJW:RWT6:FJHH:7THB:2FTM:KFSB:5GNH:K42Q:QCY6
     Docker Root Dir: /var/lib/docker
     Debug Mode: false
     Username: zinic2
     Registry: https://index.docker.io/v1/
     Experimental: false
     Insecure Registries:
      127.0.0.0/8
     Live Restore Enabled: false
    ```

#### Docker 데몬 상태

위 `docker info`에서 서버 버전 `23.0.3`과 당시 실행 중인 컨테이너 수 `2`를 읽을 수 있다. 이는 해당 CLI가 엔진에서 응답을 받았다는 근거다. Ubuntu 호스트의 서비스 상태와 시작 로그는 별도로 수집한다. `systemctl status docker.service`의 `Active:` 행에서 실행 상태를, `journalctl -u docker.service`에서 시작 시각이나 실패 원인을 확인한다. [데몬 상태 수집 명령](#docker-replay)

2026-11-21 편집 환경에서는 PID 1이 `bash`였고 Docker CLI와 `/var/run/docker.sock`이 없었다. 아래 Docker 절차 중 **수집 대기**로 표시한 출력은 Docker가 실행 중인 호스트에서 확보해야 한다. [환경 확인 로그](./evidence/terminal-session.txt)

<a id="step-4"></a>

### (4) Docker Build / Execution - Build and run a Docker image using a Dockerfile

**이미지와 컨테이너**

`docker build`는 Dockerfile과 빌드 디렉토리의 파일로 읽기 전용 이미지(`my-web:1.0`)를 만든다. `docker run`은 이 이미지에서 독립된 쓰기 계층과 프로세스를 가진 컨테이너를 생성하고 시작한다. 같은 이미지로 여러 컨테이너를 띄울 수 있다. 컨테이너를 중지했다 다시 시작하면 쓰기 계층이 유지되고, 컨테이너를 삭제하면 그 계층도 사라진다. 원본 이미지와 별도로 마운트한 이름 있는 볼륨은 남는다.

#### nginx

- **설명**

    - 정적 파일 서빙, 리버스 프록시 등에 널리 쓰이는 경량 웹 서버
    - 여기서는 `nginx:alpine` 이미지를 베이스로 정적 페이지(`site/`)를 서빙하는 커스텀 이미지를 만드는 데 사용

#### Dockerfile

- **설명**

    - nginx 베이스 이미지에 정적 페이지(`site/`)를 담아 이미지를 정의하는 Dockerfile

- **파일 내용**

    ```dockerfile
    FROM nginx:alpine
    LABEL org.opencontainers.image.title="my-custom-nginx"
    ENV APP_ENV=dev
    COPY site/ /usr/share/nginx/html/
    ```

#### docker image build

- **설명**

    - 정적 페이지 파일을 준비하고, 위 Dockerfile을 기반으로 `my-web:1.0` 이미지를 빌드

- **실행 기록**

    ```bash
    $ mkdir site
    $ echo "Hello World!" > site/index.html
    $ docker build -t my-web:1.0 .
    ```

아래 이미지 목록에서 빌드 결과의 태그와 ID를 확인할 수 있다. 단계별 빌드 출력은 수집 대기다. 재현 절차의 `--progress=plain`은 `COPY`, 이미지 내보내기, 태그 지정 단계를 텍스트로 남긴다. [빌드 입력 생성 및 로그 수집](#docker-build-replay)

#### docker images

- **실행 기록**

    ```text
    $ docker images
    REPOSITORY   TAG   IMAGE ID       CREATED       SIZE
    my-web       1.0   a0311a9bdaae   3 hours ago   62.4MB
    ```

- **전사한 원본 화면** (캡처에 보이는 한 행)

    <img src="./images/docker-images.png" width="900">

<a id="step-5"></a>

### (5) "hello-world" Execution - Run the hello-world container

`docker run hello-world`는 전용 테스트 이미지를 필요하면 내려받고, 그 이미지의 프로그램을 실행해 터미널에 메시지를 출력한다. 성공 여부는 **같은 컨테이너**의 표준 출력에 `Hello from Docker!`가 있고 종료 코드가 `0`인지로 판정한다. 이 컨테이너는 메시지를 출력한 뒤 종료되므로 `docker ps -a`에서 확인한다.

**수집 대기** — 전용 이미지의 실행 로그·화면 캡처와 당시의 `docker ps -a` 전체 출력. 기존 두 웹 컨테이너의 화면은 다음 절에 있다. [hello-world 실행, 전체 목록 저장 및 화면 캡처 방법](#docker-hello-replay)

<a id="step-6"></a>

### (6) Port Mapping & Access - Access containers through port mapping (2 times)

기본 브리지 네트워크에서 각 컨테이너는 네트워크 네임스페이스를 갖는다. 네임스페이스마다 인터페이스, 라우팅 테이블과 포트 공간이 분리되어 두 nginx가 각자 `80/tcp`를 사용할 수 있다. `-p 8080:80`은 **호스트의 8080 → 첫 번째 컨테이너의 80**, `-p 8081:80`은 **호스트의 8081 → 두 번째 컨테이너의 80**으로 연결한다. Docker가 호스트에 설정한 포트 전달 규칙 덕분에 브라우저는 컨테이너 IP를 몰라도 된다. [Docker 포트 게시 문서](https://docs.docker.com/engine/network/port-publishing/)

#### docker container run

- **설명**

    - 같은 이미지로 컨테이너 2개를 생성하되, 호스트 포트를 각각 8080/8081로 다르게 매핑하여 동시 실행

- **실행 기록**

    ```bash
    $ docker run -d -p 8080:80 --name my-container my-web:1.0
    a965487c796cc8614f70aee1c92b03c15ebaf7529471801152118aada2b8ef89
    $ docker run -d -p 8081:80 --name my-container2 my-web:1.0
    410faac2f186091ad11660983fc2e5bd977a20355e2d47c0e253fe8a94e7764a
    ```

#### docker ps (process status)

- **설명**

    - 현재 실행 중인 컨테이너 목록과 포트 매핑 상태를 확인
    - `docker ps`: 실행 중인(running) 컨테이너만 표시
    - `docker ps -a`: 중지된(stopped) 컨테이너까지 전부 표시 (`-a`: all)

- **실행 기록**

    ```bash
    $ docker ps
    ```

- **결과 화면**

    <img src="./images/docker-containers.png" width="900">

기존 `docker ps` 화면에서 웹 컨테이너를 식별하는 열을 옮기면 다음과 같다. 이 화면의 두 행은 실행 중이던 컨테이너다.

| 컨테이너 ID | 이름 | 호스트 IPv4 포트 → 컨테이너 포트 |
| --- | --- | --- |
| `a965487c796c` | `my-container` | `0.0.0.0:8080 → 80/tcp` |
| `410faac2f186` | `my-container2` | `0.0.0.0:8081 → 80/tcp` |

#### Browser Access

- **설명**

    - 두 컨테이너가 각자 매핑된 포트로 정상 응답하는지 브라우저로 접속하여 확인

- **결과 화면**

    | port 8080 | port 8081 |
    |---|---|
    | <img src="./images/docker-web.png" width="400"> | <img src="./images/docker-web2.png" width="400"> |

#### curl

- **설명**

    - 브라우저 대신 `curl`로 각 포트에 HTTP 요청을 보내 응답 내용을 확인

- **실행 기록**

    ```bash
    $ curl localhost:8080
    Hello World!
    $ curl http://localhost:8081
    Hello World!
    ```

두 응답 본문은 각각 위 브라우저 캡처와 일치한다. 두 포트의 HTTP 상태 줄과 헤더는 수집 대기다. 재현 시 각 주소에 `curl -I`로 HEAD 요청을 보내고, 상태 코드 `200`과 `Content-Type`을 본문과 별도로 확인한다. [포트별 재현 및 헤더 수집 명령](#docker-ports-replay)

#### docker logs

- **특정 컨테이너의 nginx 요청 로그 확인**

    ```bash
    docker logs my-container
    docker logs my-container2
    ```

#### docker stats

- **실행 기록**

    ```bash
    $ docker stats
    ```

- **결과 화면**

    <img src="./images/docker-stats.png" width="900">

<a id="step-7"></a>

### (7) Bind Mount - Verify bind mount synchronization

바인드 마운트는 **Docker 데몬이 실행되는 호스트의 경로**를 컨테이너 안의 절대경로에 연결한다. 예를 들어 빌드 실습에서 만든 `"$build_dir/site"`를 호스트의 원본으로, `/usr/share/nginx/html`을 nginx가 읽는 컨테이너 경로로 지정할 수 있다. `./site`는 명령을 시작한 디렉토리에 따라 가리키는 대상이 달라진다. 아래는 `mktemp`로 호스트 디렉토리를 먼저 만들고 그 절대경로를 `src`로 사용한다. [Docker 바인드 마운트 문서](https://docs.docker.com/engine/storage/bind-mounts/)

**양방향 변경 확인 (수집 대기)**

Docker 호스트에서 [로그 디렉토리를 준비한 뒤](#docker-replay) 아래 블록을 실행한다. 실행 중인 컨테이너에 호스트가 쓴 한 줄이 보이는지, 컨테이너가 덧붙인 두 번째 줄이 호스트에도 보이는지 차례로 확인한다.

```bash
bind_dir=$(mktemp -d /tmp/dev-setup-bind.XXXXXX)
bind_id=$(docker run -d --rm --mount "type=bind,src=$bind_dir,dst=/work" ubuntu sleep infinity)
docker inspect --format '{{range .Mounts}}{{.Type}} {{.Source}} -> {{.Destination}} rw={{.RW}}{{end}}' "$bind_id"

printf 'from host\n' > "$bind_dir/hello.txt"
docker exec "$bind_id" cat /work/hello.txt | tee "$log_dir/bind-container.txt"
docker exec "$bind_id" sh -c 'printf "from container\n" >> /work/hello.txt'
cat "$bind_dir/hello.txt" | tee "$log_dir/bind-host.txt"
docker stop "$bind_id"
```

| 선택 기준 | 바인드 마운트 | 이름 있는 볼륨 |
| --- | --- | --- |
| 저장 위치 | 호스트 디렉토리를 경로로 지정한다. | Docker가 관리하는 저장소를 이름으로 지정한다. |
| 적합한 용도 | 소스·정적 파일을 편집하면서 변경을 바로 확인할 때 | 컨테이너를 교체해도 앱 데이터를 다시 연결할 때 |
| 운영 시 고려할 점 | 다른 호스트에서는 경로를 맞춰야 한다. 쓰기가 필요 없으면 `readonly`로 연결한다. | 호스트 경로에 덜 의존한다. 파일 확인과 백업에는 볼륨을 마운트하는 절차가 필요하다. |

**호스트 경로의 백업·복구 실측.** 바인드 마운트의 원본 디렉토리는 호스트의 `tar`로 백업할 수 있다. 임시 디렉토리 `source/`에 파일을 쓰고 `tar -C source -czf data.tar.gz .`로 백업한 뒤, 빈 `restored/`에 `tar -C restored -xzf data.tar.gz`로 복원했다. `diff -r`에서 차이가 없었고 두 파일의 SHA-256도 일치했다. 이 검증은 호스트 파일시스템에서 수행했다. [명령과 해시 원본](./evidence/host-checks.txt)

<details>
<summary>포트 충돌·백업 복구 로그 전체 보기</summary>

```text
date -Is
2026-11-21T19:29:33+09:00
# 루프백의 임시 TCP 포트를 점유한 실습 프로세스로 충돌과 해제를 재현한다.
python3 -u - <<'PORT'
import errno
import socket
import subprocess
import sys

child_code = '''
import socket
with socket.socket() as listener:
    listener.bind(("127.0.0.1", 0))
    listener.listen()
    print(listener.getsockname()[1], flush=True)
    input()
'''
with subprocess.Popen([sys.executable, "-u", "-c", child_code],
                      stdin=subprocess.PIPE, stdout=subprocess.PIPE, text=True) as child:
    try:
        port = int(child.stdout.readline())
        address = ("127.0.0.1", port)
        print(f"LISTEN 127.0.0.1:{port} PID={child.pid}")
        subprocess.run(["ps", "-p", str(child.pid), "-o", "pid,comm"], check=True)
        with socket.socket() as second:
            try:
                second.bind(address)
            except OSError as error:
                if error.errno != errno.EADDRINUSE:
                    raise
                print(f"두 번째 bind 실패: {error}")
            else:
                raise RuntimeError("예상한 포트 충돌이 발생하지 않음")
    finally:
        child.communicate("\n", timeout=5)
    print(f"점유 프로세스 종료 코드: {child.returncode}")
    if child.returncode:
        raise RuntimeError("점유 프로세스 비정상 종료")

with socket.socket() as recovered:
    recovered.bind(address)
    recovered.listen()
    print(f"동일 포트 bind 및 listen 성공: 127.0.0.1:{port}")
PORT
LISTEN 127.0.0.1:45901 PID=254223
    PID COMMAND
 254223 python3
두 번째 bind 실패: [Errno 98] Address already in use
점유 프로세스 종료 코드: 0
동일 포트 bind 및 listen 성공: 127.0.0.1:45901

# 호스트 디렉토리를 빈 경로에 복원하고 파일 전체를 대조한다.
mkdir source restored
printf 'from host\n' > source/hello.txt
tar -C source -czf data.tar.gz .
tar -tzf data.tar.gz
./
./hello.txt
tar -C restored -xzf data.tar.gz
sha256sum source/hello.txt restored/hello.txt
1104b7891b40673f4b6f5cc5e09be12a68ff13567648ae1510fae709e2df7115  source/hello.txt
1104b7891b40673f4b6f5cc5e09be12a68ff13567648ae1510fae709e2df7115  restored/hello.txt
diff -r source restored
cat restored/hello.txt
from host
```

</details>

<a id="step-8"></a>

### (8) Volume - Verify Docker volume persistence

#### docker volume

- **설명**

    - 이름 있는 볼륨(`mydata`)을 생성해 컨테이너에 마운트하고 파일을 기록한 뒤, 컨테이너를 삭제하고 같은 볼륨을 새 컨테이너에 다시 마운트하여 데이터가 유지되는지 확인

- **실행 기록**

    ```bash
    $ docker volume create mydata
    mydata
    $ docker run -d --name vol-test -v mydata:/data ubuntu sleep infinity
    bc1c2bba2f0dd0040975dcf292cae58c772f22bce83a5b7b04795b2d923cd385
    $ docker exec -it vol-test bash -lc "echo hi > /data/hello.txt && cat /data/hello.txt"
    hi
    $ docker rm -f vol-test
    vol-test

    $ docker run -d --name vol-test2 -v mydata:/data ubuntu sleep infinity
    0c3a49ade617c8cc2b3b07c95f4b7e23103f3906c10d9db94b80725ce3604aac
    $ docker exec -it vol-test2 bash -lc "cat /data/hello.txt"
    hi
    $ docker rm -f vol-test2
    vol-test2
    ```

#### 볼륨 백업과 새 볼륨으로 복구

위 실습에서는 첫 번째 컨테이너를 삭제한 뒤에도 두 번째 컨테이너가 `hi`를 읽었다. 볼륨 자체를 잃는 경우에 대비하려면 데이터를 별도로 백업해야 한다. 다음 절차는 쓰기 작업이 끝난 실습 볼륨을 읽기 전용으로 열고, 호스트에 압축 파일을 만든 뒤 **새 빈 볼륨**에 복원한다. [Docker 볼륨 백업·복구 문서](https://docs.docker.com/engine/storage/volumes/#back-up-restore-or-migrate-data-volumes)

**재현 절차 (복구 실행 기록 수집 대기)**

원본은 위 실습의 `mydata`다. 아래 [볼륨 재현 명령](#docker-volume-replay)을 같은 셸에서 먼저 실행했다면 그 명령이 만든 `lab_volume`을 사용한다.

```bash
(
  set -e
  source_volume=${lab_volume:-mydata}
  docker volume inspect "$source_volume" >/dev/null
  backup_dir=$(mktemp -d "$HOME/dev-setup-backup.XXXXXX")
  archive="$backup_dir/data.tar.gz"
  docker run --rm -v "$source_volume:/data:ro" ubuntu tar -C /data -czf - . > "$archive"
  tar -tzf "$archive"

  restored_volume=$(docker volume create)
  docker run --rm -i -v "$restored_volume:/data" ubuntu tar -C /data -xzf - < "$archive"
  docker run --rm -v "$source_volume:/original:ro" -v "$restored_volume:/restored:ro" \
    ubuntu sh -ec 'diff -r /original /restored && cat /restored/hello.txt'
  printf '백업 파일 %s\n복구 볼륨 %s\n' "$archive" "$restored_volume"
)
```

압축 목록에 `./hello.txt`가 있고 `diff -r`가 차이 없이 끝나 복구본에서 `hi`가 출력되면 실습 데이터가 복원된 것이다. 호스트 디스크 장애까지 대비하려면 검증한 압축 파일을 다른 저장장치에도 복사한다.

<a id="step-9"></a>

### (9) Git & VSCode Github - Configure Git and connect VS Code to GitHub

#### 현재 저장소의 커밋과 push 기록 (2026-11-21, 한국시간)

`/root/Dev_Setup`의 커밋 시각은 **19:04:12**, 원격 추적 브랜치의 reflog에 `update by push`가 기록된 시각은 **19:04:18**이다. GitHub에서 조회한 `master`의 커밋 ID도 로컬과 일치했다. [명령과 출력 원본](./evidence/terminal-session.txt)

```text
$ TZ=Asia/Seoul git log -1 --date=iso-local --format='%h %cd %s'
7a2e4f3 2026-11-21 19:04:12 +0900 update dev setup
$ TZ=Asia/Seoul git reflog show -1 --date=iso-local --format='%h %gd %gs' refs/remotes/origin/master
7a2e4f3 origin/master@{2026-11-21 19:04:18 +0900} update by push
$ git rev-parse HEAD
7a2e4f34cc2d68774b5fca1a22e65bc994d1761f
$ git ls-remote origin refs/heads/master
7a2e4f34cc2d68774b5fca1a22e65bc994d1761f	refs/heads/master
```

아래 `main`과 `4f5470c`가 나오는 세 블록은 이전 실습 기록이다.

#### git config --list - Check Git Configuration

- **설명**

    - Git에 적용된 전체 설정을 출력해 사용자 정보(`user.name`, `user.email`)가 등록되어 있는지 확인 (이메일은 일부 마스킹)
    - `credential.helper`가 VS Code의 `git-credential-helper`로 지정되어 있어, Git 인증은 VS Code에 로그인한 GitHub 계정으로 처리됨

- **실행 기록**

    ```bash
    $ git config --list
    credential.helper=!f() { /root/.vscode-server/bin/2c33b50b16c472efe58357d1d2a8a5414bc23fbf/node /tmp/vscode-remote-containers-c4cd4e45-8110-4af8-9854-4faba6ed526a.js git-credential-helper $*; }; f
    safe.directory=*
    user.name=eunhye0323
    user.email=vit0***@naver.com
    filter.lfs.clean=git-lfs clean -- %f
    filter.lfs.smudge=git-lfs smudge -- %f
    filter.lfs.process=git-lfs filter-process
    filter.lfs.required=true
    credential.helper=!f() { /root/.vscode-server/bin/2c33b50b16c472efe58357d1d2a8a5414bc23fbf/node /tmp/vscode-remote-containers-c4cd4e45-8110-4af8-9854-4faba6ed526a.js git-credential-helper $*; }; f
    core.repositoryformatversion=0
    core.filemode=true
    core.bare=false
    core.logallrefupdates=true
    remote.origin.url=https://github.com/eunhye0323/dev_setup.git
    remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
    branch.main.remote=origin
    branch.main.merge=refs/heads/main
    branch.main.vscode-merge-base=origin/main
    ```

#### git branch -vv - Check Branch

- **설명**

    - 현재 브랜치가 `main`이고 원격 브랜치 `origin/main`을 추적하는지 확인

- **실행 기록**

    ```bash
    $ git branch -vv
    * main 4f5470c [origin/main] update
    ```

#### git remote - Check GitHub Repository Link

- **설명**

    - 연결된 원격 저장소(GitHub)를 확인하고, GitHub의 `main` 최신 커밋이 로컬 `main`과 같은지 비교해 push까지 반영되었음을 확인

- **실행 기록**

    ```bash
    $ git remote -v
    origin	https://github.com/eunhye0323/dev_setup.git (fetch)
    origin	https://github.com/eunhye0323/dev_setup.git (push)
    $ git rev-parse main
    4f5470c2567a61893dc283980dd5038b02caed3f
    $ git ls-remote origin refs/heads/main
    4f5470c2567a61893dc283980dd5038b02caed3f	refs/heads/main
    ```

<a id="docker-replay"></a>

## 4) Docker 실습 재현 및 증거 수집

Docker 엔진에 연결된 **Ubuntu 호스트의 저장소 루트**에서 같은 Bash 세션으로 아래 블록을 순서대로 실행한다. `log_dir`는 실행마다 새로 만드는 기록 폴더다. `tee`는 화면에 보인 출력을 파일에도 남기고, `pipefail`은 앞쪽 명령이 실패한 경우 그 상태를 전달한다.

```bash
mkdir -p evidence
log_dir=$(mktemp -d "$PWD/evidence/docker-XXXXXX")
printf '실행 기록 폴더 %s\n' "$log_dir"
set -o pipefail
docker version 2>&1 | tee "$log_dir/engine.txt"
```

`docker version`에 `Server` 정보가 있는지 먼저 확인한다. systemd가 Docker를 관리하는 호스트에서는 다음 두 명령으로 서비스 상태와 최근 데몬 로그도 저장한다.

```bash
systemctl status docker.service --no-pager --full 2>&1 | tee "$log_dir/daemon-status.txt"
journalctl -u docker.service -n 20 --no-pager 2>&1 | tee "$log_dir/daemon-journal.txt"
```

<a id="docker-build-replay"></a>

### 이미지 빌드와 성공 단계 기록

```bash
build_dir=$(mktemp -d /tmp/dev-setup-build.XXXXXX)
mkdir "$build_dir/site"
printf 'Hello World!\n' > "$build_dir/site/index.html"
cat > "$build_dir/Dockerfile" <<'DOCKERFILE'
FROM nginx:alpine
LABEL org.opencontainers.image.title="my-custom-nginx"
ENV APP_ENV=dev
COPY site/ /usr/share/nginx/html/
DOCKERFILE

DOCKER_BUILDKIT=1 docker build --progress=plain -t my-web:1.0 "$build_dir" 2>&1 | tee "$log_dir/build.txt" &&
  docker image inspect --format '{{.Id}} {{json .RepoTags}}' my-web:1.0 | tee "$log_dir/built-image.txt"
```

빌드 로그에서 `site/` 복사와 이미지 내보내기 단계의 완료 또는 캐시 사용을 확인한다. 이어서 기록한 이미지 ID와 태그는 다음 포트 실습에서 어떤 이미지를 실행했는지 확인하는 기준이다.

<a id="docker-hello-replay"></a>

### hello-world 실행 및 전체 목록

```bash
docker run --cidfile "$log_dir/hello.cid" hello-world 2>&1 | tee "$log_dir/hello-world.txt" &&
  docker inspect --format '{{.Id}} {{.Config.Image}} {{.State.Status}} exit={{.State.ExitCode}}' \
    "$(cat "$log_dir/hello.cid")" | tee "$log_dir/hello-state.txt"
docker ps -a | tee "$log_dir/containers-all.txt"
docker images | tee "$log_dir/images.txt"
```

`--cidfile`에 이번 컨테이너 ID를 저장한다. 종료 후에도 목록에 남도록 여기서는 `--rm`을 생략한다. 터미널에서 실행 명령, `Hello from Docker!`, 같은 ID로 조회한 `exited exit=0`이 보이는 화면을 캡처해 기록 폴더에 `hello-world.png`로 저장한다. `containers-all.txt`에서 ID 앞 12자리가 일치하는 `Exited (0)` 행을 확인한다. 제출할 때는 (5)에 화면 캡처를 첨부하고 `containers-all.txt`와 `images.txt`의 전체 출력을 텍스트 블록으로 넣는다.

<a id="docker-ports-replay"></a>

### 포트별 재현 — 8080과 8081

두 호스트 포트가 비어 있을 때 실행한다. `127.0.0.1`을 명시해 이 실습의 접속 주소를 호스트의 루프백으로 지정한다. 각 포트에 대해 Docker가 게시한 주소, HEAD 응답의 상태·헤더, GET 응답 본문을 따로 저장한다.

```bash
(
  set -e
  for port in 8080 8081; do
    docker run --rm -d --cidfile "$log_dir/web-$port.cid" -p "127.0.0.1:$port:80" my-web:1.0
    docker port "$(cat "$log_dir/web-$port.cid")" 80/tcp | tee "$log_dir/port-$port.txt"
    curl --retry 5 --retry-connrefused --retry-delay 1 -fsSI "http://127.0.0.1:$port/" | tee "$log_dir/headers-$port.txt"
    curl -fsS "http://127.0.0.1:$port/" | tee "$log_dir/body-$port.txt"
  done
  docker ps -a | tee "$log_dir/containers-with-web.txt"
)
```

각 헤더의 상태 코드가 `200`이고 두 본문이 모두 `Hello World!`인지 확인한다. 브라우저 확인까지 끝난 뒤에는 방금 생성한 두 컨테이너의 ID로 포트를 해제한다.

```bash
for port in 8080 8081; do
  cidfile="$log_dir/web-$port.cid"
  if [ -s "$cidfile" ]; then
    docker stop "$(cat "$cidfile")"
  fi
done
```

<a id="docker-volume-replay"></a>

### 볼륨 재현 — 컨테이너 교체 후 다시 읽기

```bash
lab_volume="dev-setup-${log_dir##*/}"
docker volume create "$lab_volume"
docker run --rm -v "$lab_volume:/data" ubuntu sh -c 'printf "hi\n" > /data/hello.txt'
docker run --rm -v "$lab_volume:/data:ro" ubuntu cat /data/hello.txt | tee "$log_dir/volume-read.txt"
```

첫 번째 컨테이너는 명령을 마치며 삭제된다. 새 컨테이너가 같은 볼륨을 읽기 전용으로 연결해 `hi`를 읽으면 지속성이 확인된다. 같은 세션에서 저장 방식을 비교하거나 복구를 시험할 수 있다. [바인드 마운트 양방향 실습](#step-7) · [백업 및 복구](#step-8)

<a id="troubleshooting"></a>

## 5) 트러블슈팅

### 호스트 포트 8080을 이미 사용 중일 때

아래는 `docker run -p …`에서 `port is already allocated` 또는 `address already in use`를 만났을 때의 진단 절차다.

1. **점유자 확인.** 호스트에서 리스닝 소켓의 PID·프로그램과 실행 중인 컨테이너의 포트 매핑을 함께 본다. `PORTS`에서 화살표 왼쪽이 `:8080`인 행을 찾는다.

   ```bash
   sudo ss -ltnp 'sport = :8080'
   docker ps --format 'table {{.Names}}\t{{.Ports}}'
   ```

2. **포트 해제 또는 변경.** 점유자가 종료해도 되는 본인의 실습 컨테이너 `my-container`라면 `docker stop my-container`로 해제한다. 직접 띄운 호스트 프로세스라면 해당 터미널에서 `Ctrl+C`로 종료한다. 기존 서비스를 계속 운영해야 하면 새 실습의 호스트 포트를 `8082`로 바꾼다.

3. **재검증.** 1단계 명령으로 선택한 포트의 점유 상태를 다시 확인한다(`8082`를 선택했다면 `sport = :8082`). 아래 예시는 대체 포트를 사용한다. `docker port`가 선택한 호스트 포트를 표시하고 `curl`이 HTTP 200을 반환하면 해결된 것이다.

   ```bash
   port=8082
   web_id=$(docker run --rm -d -p "127.0.0.1:$port:80" my-web:1.0)
   docker port "$web_id" 80/tcp
   curl --retry 5 --retry-connrefused --retry-delay 1 -fsSI "http://127.0.0.1:$port/"
   docker stop "$web_id"
   ```

**호스트에서 재현한 결과.** 편집 환경에서 Python 프로세스가 루프백의 임시 TCP 포트를 열게 한 뒤 같은 주소와 포트를 다시 열었다. 점유 프로세스가 실행 중일 때는 두 번째 `bind`가 실패했고, 해당 프로세스가 정상 종료한 뒤 같은 포트에서 `listen`까지 성공했다. [실제 명령과 출력](./evidence/host-checks.txt)

```text
LISTEN 127.0.0.1:45901 PID=254223
두 번째 bind 실패: [Errno 98] Address already in use
점유 프로세스 종료 코드: 0
동일 포트 bind 및 listen 성공: 127.0.0.1:45901
```

### 실제 사례 — 홈 디렉토리에서 Git 원격 설정 실패

| 단계 | 확인한 내용 |
| --- | --- |
| 증상 | `/root`에서 `git remote add origin …` 실행 시 `fatal: not a git repository`가 출력됐다. |
| 가설 | 현재 디렉토리가 프로젝트 작업 트리 밖이어서 Git이 저장소를 찾을 수 없다. |
| 검증 | 같은 위치에서 읽기 전용인 `git rev-parse --show-toplevel`을 실행해 같은 오류를 재현했다. |
| 조치와 결과 | `cd /root/Dev_Setup` 후 같은 명령이 저장소 루트를 출력했다. `git remote get-url origin`으로 기존 연결을 확인했고 로컬과 원격의 커밋 ID도 일치했다. |

```text
$ cd /root
$ git rev-parse --show-toplevel
fatal: not a git repository (or any parent up to mount point /)
Stopping at filesystem boundary (GIT_DISCOVERY_ACROSS_FILESYSTEM not set).
$ cd /root/Dev_Setup
$ git rev-parse --show-toplevel
/root/Dev_Setup
$ git remote get-url origin
https://github.com/eunhye0323/Dev_Setup.git
```

실패한 위치부터 복구 후 브랜치·원격 확인까지 한 세션에 기록했다. [실제 진단 로그](./evidence/terminal-session.txt)

