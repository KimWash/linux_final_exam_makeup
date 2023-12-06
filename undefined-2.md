# 웹 서버

## nginx

nginx는 아파치와 함께 웹서버 중 하나로 이미지, 동영상, 자바스크립트, HTML 등 다양한 문서를 제공하는 서버 시스템으로, HTTP 통신 프로토콜을 통해 리소스를 전달한다.

```bash
sudo apt update
sudo apt install nginx
sudo ufw allow 'Nginx HTTP'
sudo systemctl status nginx # 상태 확인 
```

### 프록시로서의 nginx

클라이언트가 웹서버에 요청을 보내면 중간에서 프록시 서버가 요청을 가로챈다. 방문하고자 하는 웹사이트에 직접적으로 방문하는 것을 방지

### 리버스 프록시

여러 개의 서버 앞에 두고, 특정 서버가 과부화되지 않게 로드 밸런싱을 수행하는 서버로, 웹서버의 IP 주소를 노출시킬 필요가 없다는 것도 장점이다. 특히, 나는 하나의 80 포트를 listening 하는 nginx 서버에서 서브도메인 별로 다른 웹서버/서비스를 이용하게 하고 싶어서 리버스 프록시를 이용했다.

### systemctl 명령

stop/start/restart/reload/disable/enable 명령

### nginx configuration

`/etc/nginx/sites-available` 디렉토리에 사이트들을 정의할 수 있다. configuration 파일은 다음과 같은 요소들을 포함할 수 있다.

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name dweb;
    
    root /var/dweb/
    index index.html;
    
    location /songdo {
        proxy_pass http://172.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

* `server` 블록
  * `listen` : 어떤 URI/포트의 요청을 이 사이트로 넘길 것인지를 선언한다.
  * `server_name` :  Host Header가  담고 있는 필드값과 일치한 도메인명을 가진    요청에 대해서만 허용하게 할 수 있다. wild card 와 정규 표현식 모두 이용 가능하다.
  * `root` : static file이 있는 파일 시스템의 경로이다.
  * `location` 블록: 경로에 따라 요청을 어떻게 처리할 지 결정할 수 있다. static file을 보여주거나 프록시 서버로 요청을 전송할 수 있다. 아래 예제와 같이 설정하면 `/songdo` path로 요청하는 경우 localhost의 3000번 포트로 연결해주는 것이다.

이렇게 설정 파일을 생성하고 `/etc/nginx/sites-enabled` 를 타겟으로 심볼릭 링크를 생성한다. 이로서 설정 파일을 활성화 한 것이다. 아래는 심볼릭 링크 생성을 포함해 nginx를 재시작하는 예제이다.

```bash
ln -s /etc/nginx/sites-available/{conf_file} /etc/nginx/sites-enabled/
```

{% code title="/etc/nginx/nginx.conf" %}
```
 # server_names_hash_bucket_size 64; (# 제거)
 # include /etc/nginx/conf.d/*.conf; (# 추가)
include /etc/nginx/sites-enabled/{conf_file};
```
{% endcode %}

이렇게 설정하면 이 사이트의 루트로 접속하면 스태틱 파일을, /songdo 로 접속하면 백엔드 서버 등 다른 웹서버로 연결을 포워딩하게 된다. 이렇게 세팅한 후 리액트 프로젝트를 세팅하고 스태틱 페이지로 빌드해도 되고, express 등 서버를 구동시켜 놓을 수도 있다.&#x20;
