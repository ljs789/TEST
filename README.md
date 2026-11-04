# 워크스테이션 구축 결과물

## 1. 기술 문서
### 프로젝트 개요
터미널·Docker·Git을 직접 세팅하여 "내 컴퓨터에서만 돌아가는" 문제를 줄이고, 누구나 동일하게 실행·배포·디버깅할 수 있는 재현 가능한 개발 환경을 구축한다. 웹 서버를 컨테이너화하고 포트 매핑·볼륨으로 접속과 데이터 영속성을 직접 검증한다.

### 실행 환경
- OS: Ubuntu 22.04
- Shell: bash
- Docker: 26.x
- Git: 2.x

### 수행 항목 체크리스트
- [x] 터미널 기본 조작 및 폴더 구성
- [x] 권한 변경 실습
- [x] Docker 설치/점검
- [x] hello-world 실행
- [x] Dockerfile 빌드/실행
- [x] 포트 매핑 접속(2회)
- [x] 바인드 마운트 반영
- [x] 볼륨 영속성
- [x] Git 설정 + VSCode GitHub 연동

### 검증 방법(어떤 명령으로 무엇을 확인했는지) + 결과 위치 링크


### 트러블슈팅 2건 이상(문제 → 원인 가설 → 확인 → 해결/대안)
- docker logs 인자 누락 오류
- 증상: `docker logs` 실행 시 "requires 1 argument" 오류
- 원인: 컨테이너 이름/ID를 지정하지 않음
- 해결: `docker logs gallant_davinci` 처럼 이름 지정 후 실행



## 2. 터미널 조작 로그(터미널에서 수행한 핵심 명령과 출력 결과를 기술 문서에 기록한다.)
(핵심 명령 + 출력 결과)

## 3. Docker 운영/검증 로그
- docker --version
root@GNIA-C202-G05:~# docker --version
Docker version 29.9.3, build 5457199
→ Docker 29.9.3 설치 확인

- docker info 등 설치·점검 결과
root@GNIA-C202-G05:~# docker info
Client: Docker Engine - Community
 Version:    29.9.3
 Context:    default
 Debug Mode: false
 Plugins:
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.38.1
    Path:     /usr/libexec/docker/cli-plugins/docker-buildx
  compose: Docker Compose (Docker Inc.)
    Version:  v5.7.0
    Path:     /usr/libexec/docker/cli-plugins/docker-compose
  model: Docker Model Runner (Docker Inc.)
    Version:  v1.2.9
    Path:     /usr/libexec/docker/cli-plugins/docker-model

Server:
 Containers: 1
  Running: 0
  Paused: 0
  Stopped: 1
 Images: 1
 Server Version: 29.9.3
 Storage Driver: overlayfs
  driver-type: io.containerd.snapshotter.v1
 Logging Driver: json-file
 Cgroup Driver: systemd
 Cgroup Version: 2
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local splunk syslog
 CDI spec directories:
  /etc/cdi
  /var/run/cdi
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: b7a25261e029d68978bbf290df0a62fc819b3c51
 runc version: v1.5.2-0-g29dd3dc2
 init version: de40ad0
 Security Options:
  seccomp
   Profile: builtin
  cgroupns
 Kernel Version: 6.18.54.1-microsoft-standard-WSL2
 Operating System: Ubuntu 26.04.1 LTS
 OSType: linux
 Architecture: x86_64
 CPUs: 24
 Total Memory: 15.25GiB
 Name: GNIA-C202-G05
 ID: 9d7f44d4-81b2-4c9c-bc3e-bdb50215e3f8
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
  ::1/128
 Live Restore Enabled: false
 Firewall Backend: iptables
  EnableUserlandProxy: true
  UserlandProxyPath: /usr/bin/docker-proxy

- docker images
  root@GNIA-C202-G05:~# docker images    # 이미지 목록
                                                                                       i Info →   U  In Use
IMAGE                ID             DISK USAGE   CONTENT SIZE   EXTRA
hello-world:latest   c567b231f656       25.9kB         9.49kB    U 
→ hello-world:latest 이미지 존재 확인

- docker ps -a
root@GNIA-C202-G05:~# docker ps -a
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS     NAMES
6388aa042d79   hello-world   "/hello"   25 minutes ago   Exited (0) 25 minutes ago             gallant_davinci
→ hello-world 컨테이너가 정상 종료(Exited 0)됨

- docker logs
root@GNIA-C202-G05:~# docker logs gallant_davinci

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
→ gallant_davinci 컨테이너의 실행 로그 확인, 정상 동작 메시지 출력됨

- docker stats 
root@GNIA-C202-G05:~# docker stats --no-stream
root@GNIA-C202-G05:~#
→ 실행 중 컨테이너 없음 (정상)

## 4. Dockerfile 기반 웹 서버 컨테이너
- 웹 서버 소스코드
(html/index.html)

- Dockerfile 
FROM nginx:latest
COPY html/ /usr/share/nginx/html/
> Dockerfile: nginx 공식 이미지를 베이스로, 내 html/ 폴더를
> nginx 웹 루트에 복사하여 커스텀 이미지를 생성

- 빌드/실행 명령 및 결과 로그
root@GNIA-C202-G05:~/custom-nginx# docker build -t custom-nginx .
[+] Building 5.6s (7/7) FINISHED                                                            docker:default
 => [internal] load build definition from Dockerfile                                                  0.0s
 => => transferring dockerfile: 89B                                                                   0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                                       2.2s
 => [internal] load .dockerignore                                                                     0.0s
 => => transferring context: 2B                                                                       0.0s
 => [internal] load build context                                                                     0.1s
 => => transferring context: 324B                                                                     0.0s
 => [1/2] FROM docker.io/library/nginx:latest@sha256:5ab5b0b01097420873a3f3330f44248e34a3c2a3e8d5ee3  2.3s
 => => resolve docker.io/library/nginx:latest@sha256:5ab5b0b01097420873a3f3330f44248e34a3c2a3e8d5ee3  0.0s
 => => sha256:a670537bed7086aeb8b663aad5e09cd584e19c6598cd2b6d905c5f2f2513938b 1.40kB / 1.40kB        0.2s
 => => sha256:d322f1bf41d8e99dc47f550cbf85e79ca18bc45db12fd1b7a8b34bf79cbd7d5a 1.21kB / 1.21kB        0.4s
 => => sha256:c139fe92686de21a8c49d9cb40415d1dddd95fbbee4d696cebe37693236fe6d4 404B / 404B            0.6s
 => => sha256:6e2ab1f3ef4e0ea3bbe1d208dfa048969199e3dab99e93c051b7c2d09c8efb3e 964B / 964B            0.6s
 => => sha256:9f604dae09b216f6c87919d0e59d5a4ddc4387ef071cf63c645628f93808e37b 628B / 628B            0.4s
 => => sha256:a071113ac575190a0fee0c046bcf51632356f07419bdffaa55798f54e5b2e23b 34.05MB / 34.05MB      0.6s
 => => sha256:6fc242d221a673263eabd14a9ad9584d3177c96e5cb504c891497338e2f912f5 29.86MB / 29.86MB      0.8s
 => => extracting sha256:6fc242d221a673263eabd14a9ad9584d3177c96e5cb504c891497338e2f912f5             0.3s
 => => extracting sha256:a071113ac575190a0fee0c046bcf51632356f07419bdffaa55798f54e5b2e23b             0.3s
 => => extracting sha256:9f604dae09b216f6c87919d0e59d5a4ddc4387ef071cf63c645628f93808e37b             0.0s
 => => extracting sha256:6e2ab1f3ef4e0ea3bbe1d208dfa048969199e3dab99e93c051b7c2d09c8efb3e             0.0s
 => => extracting sha256:c139fe92686de21a8c49d9cb40415d1dddd95fbbee4d696cebe37693236fe6d4             0.0s
 => => extracting sha256:d322f1bf41d8e99dc47f550cbf85e79ca18bc45db12fd1b7a8b34bf79cbd7d5a             0.0s
 => => extracting sha256:a670537bed7086aeb8b663aad5e09cd584e19c6598cd2b6d905c5f2f2513938b             0.0s
 => [2/2] COPY html/ /usr/share/nginx/html/                                                           0.1s
 => exporting to image                                                                                0.9s
 => => exporting layers                                                                               0.1s
 => => exporting manifest sha256:0eb1da9a0fd3a21e77abb27a077f99aa102e96ceb8cc15328307687e6b1a6daa     0.0s
 => => exporting config sha256:7df2b552c3312b938902ac689365da3c017fdfbde106fd3225c3a6d5e44f21e6       0.0s
 => => exporting attestation manifest sha256:f233ee22894a4d501461b1e5844b9e2dff5453a444d1db8a3b95e18  0.0s
 => => exporting manifest list sha256:3e0c137bae80748c729bb644ab365559b487afb8869abbfb19915f63ec6741  0.0s
 => => naming to docker.io/library/custom-nginx:latest                                                0.0s
 => => unpacking to docker.io/library/custom-nginx:latest                                             0.7s
root@GNIA-C202-G05:~/custom-nginx# docker images
                                                                                       i Info →   U  In Use
IMAGE                 ID             DISK USAGE   CONTENT SIZE   EXTRA
custom-nginx:latest   3e0c137bae80        241MB         63.9MB
hello-world:latest    c567b231f656       25.9kB         9.49kB    U
- 포트 매핑 접속 성공 증거
root@GNIA-C202-G05:~/custom-nginx# docker run -d -p 8080:80 --name my-web custom-nginx
1ad40bf611b52de1348534b72035725d04a9074dac7bc3f783c33666051ae8ac
root@GNIA-C202-G05:~/custom-nginx# docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                                     NAMES
1ad40bf611b5   custom-nginx   "/docker-entrypoint.…"   5 seconds ago   Up 4 seconds   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   my-web

root@GNIA-C202-G05:~/custom-nginx# curl http://localhost:8080
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>My Custom NGINX</title>
</head>
<body>
    <h1>안녕하세요! 커스텀 NGINX 서버입니다 🚀</h1>
    <p>Docker로 빌드한 나만의 웹 서버</p>
</body>
</html>
> curl 결과: 호스트 8080 → 컨테이너 80(nginx) 포트 매핑으로
> 커스텀 index.html 정상 응답 확인 ✅

## 5. 포트 매핑 접속 증거
p <host_port>:<container_port>로 실행 후, 브라우저 접속 화면(주소창 포함)을 기술 문서에 첨부한다.

## 6. 바인드 마운트 반영 + 볼륨 영속성 증거
바인드 마운트: 실행 명령 + 호스트 변경 전/후 비교
Docker 볼륨: 생성/연결/검증 명령 + 컨테이너 삭제 전/후 비교

## 7. Git 설정 및 GitHub/VSCode 연동 증거
Git 사용자 정보·기본 브랜치 설정 후, VSCode에서 GitHub 로그인 및 저장소 연동 완료
민감한 개인 정보(ID/PW, 토큰 등)가 포함되지 않도록 주의한다