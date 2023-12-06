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
