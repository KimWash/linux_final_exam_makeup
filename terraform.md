# Terraform을 이용해 선언적으로 프로젝트별 인프라 관리하기

### 필요성

AWS를 이용해 프로젝트를 배포하려 할 때, AWS의 설정을 직접 웹 GUI를 이용해 세팅해주는 작업은 서버를 확장하거나 하는 등의 상황에서 불편하고 불필요한 실수를 만들어낼 수 있다. Terraform과 같은 IaC 도구를 이용하면 인프라를 선언적으로, 또 자동으로 관리할 수 있다.

<figure><img src=".gitbook/assets/Screenshot 2023-12-05 at 22.28.40.png" alt=""><figcaption><p>테라폼을 이용한 인스턴스 배포</p></figcaption></figure>

### 클라우드 서비스에 구애받지 않음

AWS뿐 아니라 Azure, GCP 등 클라운드 서비스들에 대한 구현을 제공하므로 어떤 클라우드 서비스를 이용하느냐에 구애받지 않는다.

<figure><img src=".gitbook/assets/Screenshot 2023-12-05 at 22.33.20.png" alt=""><figcaption></figcaption></figure>

### 방법

#### AWS 계정 생성

당연히 AWS를 이용하므로 AWS 계정을 생성해야 한다.

#### AWS API 엑세스 키 만들기

검색을 통해 **IAM 웹 콘솔**로 이동해 좌측 **사용자** 탭에서 사용자를 생성한다.

사용자에게 **보안 자격 증명 탭**에서 엑세스 키를 생성한다.

#### AWS CLI 설치 및 구성

아래 명령을 이용해 AWS CLI를 설치할 수 있다.

* ```bash
  curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
  unzip awscliv2.zip
  sudo ./aws/install
  ```
*   `aws configure`

    명령을 이용하면 액세스키, 시크릿키, 기본 리전, 기본 출력 형식을 정의해 셋업할 수 있다.&#x20;
* `cat ~/.aws/credentials` 명령을 이용해 설정 확인

#### 테라폼 설치

```bash
wget https://releases.hashicorp.com/terraform/0.14.8/terraform_0.14.8_linux_amd64.zip
ls -al
sudo apt install yum
sudo yum install -y unzip
unzip terraform_0.14.8_linux_amd64.zip
cp terraform /usr/bin/
./terraform -version
```

#### 테라폼을 이용해 인스턴스 생성/배포/제거

```bash
nano main.tf # 테라폼 설정 파일 셋업 
terraform init
terraform apply
terraform destroy
```

#### AWS EC2 Instance 배포를 위한 설정 파일 구성

* `ami`: Amazon Machine Image. 생성할 인스턴스의 이미지 ID를 지정해준다.
* `instance_type`: 인스턴스 종류
* &#x20;`tags`: 인스턴스에 부여할 태그

<div align="left">

<figure><img src=".gitbook/assets/Screenshot 2023-12-05 at 23.02.09.png" alt="" width="375"><figcaption></figcaption></figure>

</div>

<div align="left">

<figure><img src=".gitbook/assets/Screenshot 2023-12-05 at 23.01.41.png" alt="" width="375"><figcaption></figcaption></figure>

</div>

```
provider "aws" {
  region = "ap-northeast-2"
}

resource "aws_instance" "test_instance" {
  ami           = "ami-086cae3329a3f7d75"
  instance_type = "t2.micro"
  tags = {
    Name = "Test"
  }
}
```

