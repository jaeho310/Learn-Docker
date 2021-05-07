# Docker란
- 컨테이너 기반의 오픈소스 가상화 플랫폼
- 기존의 가상머신은 하드웨어를 가상화하였지만 도커는 커널을 공유하며 프로세스 실행환경을 가상화
# Docker image와 container
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
> 실행방법 : docker run [옵션] 이미지이름 [커맨드]
```
1. pull 받은 ubuntu 이미지를 이용하여 컨테이너 실행  #adsf
$ docker run -it --name my_ubuntu /bin/bash
```
옵션 | 설명 
---- | ---- 
다리 | 다리1
금 | 의 
