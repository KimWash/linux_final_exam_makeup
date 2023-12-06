# Github Action을 이용한 CI/CD와 프론트엔드 호스팅

CI/CD 파이프라인은  개발자가 새로운 버전을 배포할 때 직접 아래와 같은 과정을 거치지 않고 진행하게 해준다.

```bash
git fetch && git pull
npm i --save
npm run build
sudo systemctl restart nginx
```

#### CI

Continous Integration의 약자로, VCS 저장소에 커밋이 푸시되면 이 코드를 빌드해 오류가 없는지 확인한 후 프로젝트 구동에 필요한 의존성들과 함께 도커 컨테이너로 만들고, 이를 도커 허브같은 공개적 컨테이너 이미지 저장소나 ghcr.io같은 비공개 처리 가능한 컨테이너 이미지 저장소에 푸시하게 된다.

#### CD

Continous Devliery의 약자로, CI 과정에서 컨테이너 이미지 저장소에 푸시된 내용을 watchtower를 이용해 변화를 감지하거나 CI 과정 후 자동으로 ssh 접속해서 컨테이너 이미지 저장소의 내용을 pull 해와 컨테이너를 구동하게 된다.&#x20;

이러한 과정을 통해 개발자들은 배포에 있어서 굉장한 편리함을 가질 수 있게 되었다.

[https://github.com/KimWash/ipfs-hosting](https://github.com/KimWash/ipfs-hosting)
