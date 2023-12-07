# Docker

### Docker란

도커는 애플리케이션을 컨테이너로 사용할 수 있도록 만들어진 프로젝트로, 컨테이너 개념은 호스트 OS에 영향을 미치지 않고 애플리케이션이 구동될 수 있도록 한다.

* Docker 엔진
  * 도커의 메인 프로젝트로, 컨테이너와 관리의 주체이다.
  * 컨테이너를 제어하고, 다양한 기능들을 제공한다.

### 기존 가상화 기술과의 차이점?

VMWare나 VirtualBox와 같은 가상화 기술을 이용하게 되면 하이퍼파이저를 이용해  OS를  밑바닥부터 모두 구현하게 된다. 이렇게 하게 되면 하이퍼바이저로 인해 성능 손실이 발생하게 되며, 게스트 OS 전체를 포함하게 되므로 이미지 사이즈가 비대해지게 된다.

반면에 도커 컨테이너의 경우 리눅스의 chroot, namespace, cgroup 기능을 활용해 프로세스 단위의 격리된 환경을 구축하게 된다. 애플리케이션 구동에 필요한 라이브러리 및 바이너리만 존재하므로 이미지 크기가 감소하게 된다. 참고로, 커널은 호스트의 것을 그대로 공유하게 된다.

### 장점

호스트OS에 영향을 받을 수 있어도 영향을 주지는 못하므로 호스트에 서비스 운영에 필요한 패키지를 직접 설치하지 않아도 되고, 라이브러리 설치 등의 의존성 문제가 없어지게 돼 배포하는 상황에서 문제가 될만한 시나리오를 원천적으로 생기지 않게 해준다.

또한, 애플리케이션 간 독립성/확장성을 보장해주는데, 애플리케이션을 여러 모듈로 나눠 컨테이너 형태로 구동해 MSA를 구축할 수 있게 된다.

### 개념

#### 이미지

* 컨테이너를 생성할 때 사용하는 요소
* VM의 iso 개념과 유사
* 이름 구성: {저장소이름}/{이미지이름}:{태그}
  * ex) `KimWash/my-service:latest`
* 저장소 이름/태그 생략 가능

#### 컨테이너

* 도커 이미지로부터 컨테이너를 생성 가능
* 호스트로부터 분리되어 컨테이너 내의 작업이 호스트에 영향을 끼치지 않음

### 도커 컨테이너 생성 및 쉘 접속법

```bash
docker run -it ubuntu:22.04 # ubuntu 22.04 컨테이너 이미지 다운로드 후 실행
```

혹은 이미지를 따로 받고 컨테이너 생성 후 따로 쉘에 접속할 수도 있다.

```bash
docker pull centos:7
docker images
docker create -it --name mycentos centos:7
docker start mycentos
docker attach mycentos
```

### 도커 관련 커맨드

* `docker ps`: 현재 실행중인 컨테이너
  * `-a` : 실행중이지 않은 것 포함
* `docker rm` : 컨테이너 삭제 (실행중인 것 삭제 불가)
* `docker stop` : 컨테이너 중지
* `docker container prune` : 컨테이너 일괄 삭제
* `docker run {image_name}`
  * `-i`:  컨테이너와 상호 입출력
  * `-t` : bash 쉘 이용&#x20;
  * `-p {outbound}:{inbound}`: outbound 포트를 컨테이너 내부에서 inbound 포트로 포워딩
  * `--name {container_name}` : 컨테이너 이름 지정
  * `-e {ENV_NAME}={ENV_VALUE}` : 컨테이너에 주입할 환경변수 지정&#x20;
  * `-d` : 백그라운드
  * `-v {host_path_to_mount} {container_path_to_mount}` : 호스트의 특정 폴더를 컨테이너의 특정 폴더에 마운트 시킨다. 파일시스템을 공유할 때 이용한다.

### 도커 볼륨

도커 이미지는 read-only로, 컨테이너 계층에서 파일시스템에 변동이 생기면 컨테이너 삭제 시 초기화된다. 이를 방지하기 위해 호스트의 파일 시스템에 마운트 포인트를 두고 컨테이너에서 접근 가능하게 해주어야 한다.

* `docker volume create --name {volume_name}` : 도커에서 통합적으로 관리하는 볼륨 생성. 호스트쪽의 마운트 포인트를 생성하게 된다.
  * `docker run {image_name} -v {volume_name}:{container_path_to_mount}` 명령으로 이용 가능하다.&#x20;

### 도커 네트워크 드라이버

* 컨테이너가 외부와 통신 가능하도록 지원하며, 아래와 같은 종류들을 가진다.
  * Bridge
  * Host
  * None
  * Container
  * Overlay
*   드라이버 생성

    `docker network create --driver {type} {driver_name}`

    *   해당 드라이버를 이용한 컨테이너 생성

        `docker run --name {container_name} --net {driver_name} {image_name}`
*   드라이버 연결 및 해제

    `docker network disconnect {driver_name}`

    `docker network connect {driver_name} {container_name}`
*   호스트의 네트워크와 같은 환경 이용하기 (Host)

    `docker run --name {container_name} --net host {image_name}`

#### Container Network

*   다른 컨테이너의 내부 IP나 MAC주소와 같은 네트워크 네임스페이스를 공유할 수 있다.

    `--net container:{container_id}`

### 컨테이너 로깅

표준 출력/에러 스트림의 로그가 별도의 파일에 저장되며, `docker log {container_id_or_name}` 명령을 이용해 확인 가능하다.

### Docker Hub

Github와 같이 도커 컨테이너 이미지를 저장하는 저장소로, 도커에서 기본적으로 제공하므로 아래 명령어를 이용해 이용할 수 있다.

* &#x20;`docker search {image_name}` : 이미지를 검색한다.
* `docker commit {container_name} {image_name}:{tag}`
  * `-a {author}` : 이미지 만든 사람
  * `-m {message}` : 커밋 메시지
* `docker push {author}/{image_name}:{tag}` : 커밋 내용 저장소에 푸시
* `docker pull {author}/{image_name}:{tag}` : Docker hub 저장소에서 이미지 다운로드
* `docker inspect {image_name}:{tag}` : 이미지의 레이어를 해시값과 함께 확인할 수 있다.
* `docker history {image_name}:{tag}` : 이미지의 히스토리를 확인할 수 있다.
* `docker rmi {image_name}:{tag}` : 이미지 삭제
