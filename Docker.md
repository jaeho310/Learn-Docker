# 1.**Docker란**
- 컨테이너 기반의 오픈소스 가상화 플랫폼
- 기존의 가상머신은 하드웨어를 가상화하였지만 도커는 커널을 공유하며 프로세스 실행환경을 가상화
# **2. Docker image와 container**
## docker image
- 컨테이너를 실행하기 위한 환경을 담아놓는 곳
- 원격 이미지 저장소인 docker hub에서 이미지를 push, pull 할 수 있다.
- 이미지를 pull 받지 않고 이미지를 이용해 컨테이너를 생성할 시 dockerhub에 존재하는 이미지라면 자동으로 pull 하여 실행
```
1. 이미지 pull 받기
$ docker pull ubuntu:latest
```
## container
- 이미지를 이용해 구축하며 시스템 자원 및 네트워크를 사용할 수 있는 독립된 공간.
- 하나의 이미지로 여러개의 컨테이너를 실행 할 수 있으며 각각의 컨테이너는 독립된 공간이다.
- 다운받은 image를 이용하며 run 명령어를 이용해 실행한다. 
> **명령어 : docker run [옵션] 이미지:태그 [커맨드] [ARG]** 

옵션 | 설명 
---- | ---- 
-d | detached mode 백그라운드 모드로 실행
-it | -i와 -t를 동시에 사용한것으로 터미널 입력을 위해 사용
-v | 호스트와 컨테이너를 연결(마운트, volume)
-name | 컨테이너 이름 설정
-p | 호스트와 컨테이너의 포트를 연결
-rm | 컨테이너가 종료되면 컨테이너 자동 삭제 
<br>
```
1. pull 받은 ubuntu 이미지를 이용하여 컨테이너 실행
컨테이너 이름은 my_ubuntu이며 자동 삭제
$ docker run -it --rm --name my_ubuntu /bin/bash
```


# **3. Docker 명령어**
## image 관련 명령어
>**명령어 : docker <옵션> [ARG]**

옵션 | 설명
---- | ----
search | 이미지 검색
pull | 이미지 받기
images | 이미지 목록 확인
run | 컨테이너 실행
ps | 컨테이너 목록 확인
restart | 컨테이너 재실행
stop | 컨테이너 정지
attach | 컨테이너 접속
exec | 외부에서 컨테이너 내부 명령어 실행
rm | 컨테이너 삭제
rmi | 이미지 삭제 
build | 이미지 생성(Dockerfile 필요)

# **4. Dockerfile 작성 법 및 docker build**
## (1)Dockerfile
### 개요
- Dockerfile은 이미지를 만들기 위해 작성한다.
- docker build 명령어를 통해 Dockerfile에서 작성한 이미지를 생성할 수 있다.
### 목표
- 바뀌지 않는 환경들을 최대한 가볍게 이미지로 만들어 어떤 환경에서도 빠르게 사용하도록 한다.

### 작성법
옵션 | 설명
---- | ----
FROM | 어떤 base 이미지를 사용하는 지 기술
COPY | 호스트에서 이미지에 파일 추가
ADD | 호스트에서 이미지에 파일 추가(tar등 아카이브파일이나 압축파일은 압축을 풀어준다)
ENTRYPOINT | 빌드한 이미지를 컨테이너로 생성할때 단 한번 실행(run)
CMD | 빌드한 이미지를 생성할 및 시작할때 실행(Docker run, start) 단 하나의 CMD만 유효하다
WORKDIR | RUN 명령어가 실행되는 위치를 지정하며, 컨테이너의 위치를 지정한다
<br>

**[Example]**
``` Dockerfile
FROM ubuntu

COPY . /app

WORKDIR /app

ENTRYPOINT "./main"
```
FROM golang -> golang이미지를 가져와서 

COPY . /app -> 현재 파일시스템을 /app 아래에 복사  

WORKDIR -> 컨테이너의 workdir을 /app으로 변경  

ENTRYPOINT "./main" -> main이라는 파일 실행(main이라는 실행가능한 파일을 복사했다고 가정)

<br>

## (2) docker build
- 작성한 Dockerfile을 이용하여 이미지를 만들때 사용한다.
- 작성된 이미지는 컨테이너를 실행하기 위한 환경 및 설정이다.

>**명령어 : docker build [옵션] 이미지:태그** .

옵션 | 설명
---- | ----
-t | 이미지 태그를 남긴다.
-f | Dockerfile 선택

<br>

**[Example]**
```
$ docker build -t jh:1 .
// 생성된 이미지는 run 명령어를 통해 컨테이너를 만들어 실행
$ docker run -it --rm jh:1
```



