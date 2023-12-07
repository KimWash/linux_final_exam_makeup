# Dockerfile

### 필요성

도커 이미지 생성 과정을 선언형으로 관리해 애플리케이션의 빌드 및 배포의 자동화를 가능하게 한다.

### 예제 Dockerfile

```docker
FROM ubuntu:22.04
LABEL maintainer="INU LINUX"

RUN apt-get update
RUN apt-get install apache2 -y
ADD test.html /var/www/html
WORKDIR /var/www/html
RUN ["/bin/bash", "-c", "echo hello >> test2.html"]
EXPOSE 80
CMD apachectl -DFOREGROUND
```

* `FROM` : 대상이 되는 원본 이미지
* `LABEL` : 이미지에 붙일 레이블
* `RUN` : 컨테이너 빌드 전 실행할 명령
* `ADD {filename} {path}` : 컨테이너 빌드 전 추가할 파일
* `WORKDIR {path}` : 컨테이너 상에서 작업 디텍토리로 전환
* `EXPOSE {port}` : 특정 포트를 노출시킬 수 있다.
* `CMD` : 컨테이너를 생성하여 최초로 실행할 때 실행할 명령 (런타임)

### Multi-Stage Builds를 이용해 이미지 경량화하기

하나의 Dockerfile로 빌드 이미지와 실행 이미지를 분리해 빌드 이미지가 라이브러리 의존성에 의해 비대해지는 것을 방지할 수 있다.&#x20;

```docker
FROM golang:alpine:3.18
ADD main.go /root
WORKDIR /root
RUN go build -o /root/mainApp /root/main.go

FROM alpine:latest
WORKDIR /root
COPY --from=0 /root/mainApp
CMD ["./mainApp"]
```

### Docker Compose

여러 컨테이너가 유기적으로 동작하는 서비스를 위해 (예를 들면 DB가 필요한 서비스들) 만들어진 솔루션

* 설치

```bash
curl -L https://github.com/docker/compose/releases/download/v2.5.0/ 
docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose 
docker-compose --version
```

* Docker Compose는 yaml 파일을 이용해 컨테이너를 생성한다.

```yaml
version: "3.8"
services:
  mongo:
    image: mongo:5.0
    container_name: mongo
    environment:
docker-compose.yml
      - MONGO_INITDB_ROOT_USERNAME=dweb
      - MONGO_INITDB_ROOT_PASSWORD=1234
    restart: unless-stopped
    ports:
      - "27017:27017"
    volumes:
- ./database/dbs:/data/db
- ./database/dev.archive:/Databases/dev.archive - ./database/production:/Databases/production
  mongo-express:
    image: mongo-express
    container_name: mexpress
    environment:
- ME_CONFIG_MONGODB_SERVER=mongo
- ME_CONFIG_MONGODB_PORT=27017
- ME_CONFIG_MONGODB_AUTH_USERNAME=dweb - ME_CONFIG_MONGODB_AUTH_PASSWORD=1234 - ME_CONFIG_BASICAUTH_USERNAME=dweb
- ME_CONFIG_BASICAUTH_PASSWORD=1234
    links:
      - mongo
    restart: unless-stopped
    ports:
      - "8001:8081"
networks:
  default:
    name: mongo-express-network
```

### Dockerfile 관련 커맨드

* `docker build -t {image_name}:{tag} {target_path}` : 이미지 빌드&#x20;
* `docker-compose ps` : 현재 실행중인 도커 컴포즈 컨테이너로 생성된 컨테이너들의 상태를 확인할 수 있다.
  * `-p {project_name}` : 특정 프로젝트의 컨테이너로 한정한다.
* `docker-compose restart` : 서비스 컨테이너 재시작
* `docker-compose up` : 프로젝트의 컨테이너 모두 시작
  * `-p {project_name}` : 프로젝트 이름을 명시하면서 시작
  * `-d` : 백그라운드에서 실행
