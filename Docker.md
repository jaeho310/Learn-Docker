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
--no-cache | 이전 레이어에 같은명령어로 빌드됐던 기록이 있더라도 무시

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
문법 | 설명
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

COPY ./ /app/

WORKDIR /app

ENTRYPOINT "./main"
```
FROM golang -> golang이미지를 가져와서 

COPY ./ /app/ -> 현재 파일시스템을 /app 아래에 복사  

WORKDIR -> 컨테이너의 workdir을 /app으로 변경  

ENTRYPOINT "./main" -> main이라는 파일 실행(main이라는 실행가능한 파일을 복사했다고 가정)

<br>

## (2) docker build
- 작성한 Dockerfile을 이용하여 이미지를 만들때 사용한다.
- 작성된 이미지는 컨테이너를 실행하기 위한 환경 및 설정이다.

>**명령어 : docker build [옵션] ** .

옵션 | 설명
---- | ----
-t [이미지:태그]| 이미지의 이름과 태그를 남긴다.
-f | Dockerfile 선택

<br>

**[Example]**
```
$ docker build -t jh:1 .
// 생성된 이미지는 run 명령어를 통해 컨테이너를 만들어 실행
$ docker run -it --rm jh:1
```
# **5. 멀티스테이징**
- 최종적으로 불필요한 의존성, 라이브러리, 패키지를 제거해 이미지 자체의 용량이 커지는 것을 방지하기위해 사용

**[Example1]**
``` go
//main.go
package main

import "fmt"

func main(){
	fmt.Println("hello world") 
}
```

```Dockerfile
# Dockerfile
FROM golang:latest

ADD ./main.go /temp/

WORKDIR /temp

RUN go build -o myApp main.go

CMD "./myApp"
```
```
$ docker build -t ms:1 .
```
```
$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
ms           1         3653bceb0e96   2 seconds ago   864MB
```
**[Example2]**

Golang을 이용하여 helo world를 찍는 이미지지만 image의 용량이 864메가나 된다.  
경량이미지인 Golang:alpine 으로 base이미지를 변경하여 줄일 수 있다.

```Dockerfile
FROM golang:alpine3.10

ADD ./main.go /temp/

WORKDIR /temp

RUN go build -o myApp main.go

CMD "./myApp"
```
```
$ docker build -t ms:2 .
```
```
$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
ms           2         06bf1f2af276   17 minutes ago   361MB
ms           1         3653bceb0e96   23 minutes ago   864MB
```

**[Example3]**

golang:alpine으로 변경하였지만 해당이미지는 아직도 361MB가 된다.  
멀티스테이징을 사용하여 더 줄일 수 있다.

```Dockerfile
ARG BUILD_IMAGE=golang:alpine3.10
ARG BASE_IMAGE=alpine:3.10

FROM ${BUILD_IMAGE} AS build

ADD ./main.go /temp/

WORKDIR /temp

RUN go build -o myApp main.go

FROM ${BASE_IMAGE}

COPY --from=build /temp /temp

WORKDIR /temp

CMD "./myApp"
```
```
$ docker build -t ms:3 .
```
```
$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
ms           3         17c2b7152a83   5 seconds ago    7.59MB
ms           2         06bf1f2af276   21 minutes ago   361MB
ms           1         3653bceb0e96   26 minutes ago   864MB
```

>golang을 이용하여 hello world를 출력하는 이미지는 863MB이지만  
>멀티스테이징을 사용하면 7.39MB로 줄일 수 있다.

# 6. 포트포워딩 및 volume 실습
- 컨테이너를 실행할때 -p 옵션을 사용하여 포트포워딩이 가능하다.
- 컨테이너를 실행할때 -v 옵션을 사용하여 호스트와 컨테이너의 파일시스템을 공유 할 수 있다.

**[Example]**

/test 라는 URL으로 HTTP GET request가 오면 "test" 라는 문자열을 내려주는 백엔드 application이다.  
해당 application을 실행하기위해 tomcat을 사용하여 jar파일을 추출하였다.
```properties
<!-- application.properties -->
server.port=8080
```
포트는 8080으로 설정
```java
// spring controller
package com.example.demo.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class DemoController {
    
    @GetMapping("/test")
    public String test() {
        return "test";
    }
}
```
추출한 jar파일을 jre나 jdk가 깔린 환경으로 옮겨 웹애플리케이션을 실행할 수 있도록 Dockerfile 작성
``` Dockerfile
# Dockerfile
FROM appinair/jdk11-maven

ADD demoApplication.jar app.jar

ENTRYPOINT ["java","-jar","/app.jar"]
```

해당 application에서 나오는 로그를 D:/data에 저장, port는 호스트의 8081을 컨테이너의 8080으로 연결한 후(Dokerfile EXPOSE를 통해서도 가능)  
백그라운드로 실행  
백그라운드에서 실행하므로 관련 로그는 모두 application_log 아래에 저장되도록 설정
```
$ docker run -v D:/data/:/application_log -p 8081:8080 -d jartest
```

8081포트로 웹브라우저에서 접속 

![이미지](./웹브라우저.png)

D:\data\application.log 에 정상적으로 mount되어 로그가 저장 되는것을 확인

```log
20210507 06:21:39.507 [main] INFO  [DemoApplication:55] - Starting DemoApplication v0.0.1-SNAPSHOT using Java 11.0.1 on 9a79b239877c with PID 1 (/app.jar started by root in /) 
20210507 06:21:39.514 [main] INFO  [DemoApplication:675] - No active profile set, falling back to default profiles: default 
20210507 06:21:40.571 [main] INFO  [TomcatWebServer:108] - Tomcat initialized with port(s): 8080 (http) 
20210507 06:21:40.581 [main] INFO  [Http11NioProtocol:173] - Initializing ProtocolHandler ["http-nio-8080"] 
20210507 06:21:40.582 [main] INFO  [StandardService:173] - Starting service [Tomcat] 
20210507 06:21:40.597 [main] INFO  [StandardEngine:173] - Starting Servlet engine: [Apache Tomcat/9.0.45] 
20210507 06:21:40.638 [main] INFO  [[/]:173] - Initializing Spring embedded WebApplicationContext 
20210507 06:21:40.646 [main] INFO  [ServletWebServerApplicationContext:289] - Root WebApplicationContext: initialization completed in 1039 ms 
20210507 06:21:40.807 [main] INFO  [ThreadPoolTaskExecutor:181] - Initializing ExecutorService 'applicationTaskExecutor' 
20210507 06:21:40.926 [main] INFO  [Http11NioProtocol:173] - Starting ProtocolHandler ["http-nio-8080"] 
20210507 06:21:40.945 [main] INFO  [TomcatWebServer:220] - Tomcat started on port(s): 8080 (http) with context path '' 
20210507 06:21:40.953 [main] INFO  [DemoApplication:61] - Started DemoApplication in 1.894 seconds (JVM running for 2.377) 
20210507 06:21:56.938 [http-nio-8080-exec-1] INFO  [[/]:173] - Initializing Spring DispatcherServlet 'dispatcherServlet' 
20210507 06:21:56.940 [http-nio-8080-exec-1] INFO  [DispatcherServlet:525] - Initializing Servlet 'dispatcherServlet' 
20210507 06:21:56.942 [http-nio-8080-exec-1] INFO  [DispatcherServlet:547] - Completed initialization in 1 ms 

```

# 7. Dockerfile 캐시
- Dockrfile은 이전에 빌드했던 dockerfile에 동일한 명령어가 있다면 새로 빌드하지않고 이전에 사용한 이미지 레이어를 활용해 이미지를 생성
- --no-cache 옵션을 통해 cache 사용을 막을 수 있음

build 로그를 통해 직접 확인가능
```
$ docker build -t ms:4 .
=> CACHED [build 2/4] ADD ./main.go /temp/        
=> CACHED [build 3/4] WORKDIR /temp
=> CACHED [build 4/4] RUN go build -o myApp main.go  
=> CACHED [stage-1 2/3] COPY --from=build /temp /temp 
=> CACHED [stage-1 3/3] WORKDIR /temp
 ```