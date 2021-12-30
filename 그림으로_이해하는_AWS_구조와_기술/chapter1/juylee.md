# 1장 아마존 웹 서비스 기초 지식
AWS의 특징과 기본 구조 및 장점
</br></br></br>

## 1.1 아마존 웹 서비스란: 아마존이 제공하는 클라우드 서비스
### 아마존 웹 서비스(AWS: amazon Web Services)란
클라우드 컴퓨팅 서비스란 서버 및 네트워크 등을 인터넷으로 빌려주는 서비스로, 언제 어디서든지 사용할 수 있다.</br>
AWS에서는 컴퓨팅, 스토리지, 데이터베이스, 분석, 네트워킹, 모바일, 개발자 도구, 관리 도구, IoT, 보안, 엔터프라이즈 애플리케이션 등 다양한 서비스를 제공해주며,
AWS에서 제공해주는 이런 다양한 서비스들을 조합하여 모든 애플리케이션과 인프라를 구축할 수 있다.
</br></br>
### AWS 특징
1. 시스템 운영에 필요한 서비스 일체를 사용할 수 있다.</br>
  인프라와 운영 체제, 웹 서버, DB 서버 등 필요한 서비스들을 AWS에서 모두 사용할 수 있다.
2. 서비스를 조합하기 쉽다.</br>
  AWS내 뿐만 아니라 외부와도 연결해 사용할 수 있다.
3. 종량제이므로 사용할 만큼만 빌릴 수 있다.
4. 전문가가 아니어도 사용할 수 있다.
5. 한국어와 한국 원화 결제가 가능하다.</br>
  APN(Amazon Partner Network) 파트너 기업도 존재해서 도움을 받을 수 있다.
6. 보안 기준을 준수 하고 있다.</br>
  ISMS(한국 정보보호 관리체계) 인증을 취득한 상태이다.
7. 글로벌 확장이 쉽다.</br>
  AWS는 전 세계 21개 리전의 66개 가용 영역에서 운영되고 있다.
</br></br>

## 1.2 AWS의 서비스: 165개 이상의 서비스 제공
* Amazon EC2(Amazon Elastic Computer Cloud)</br>
  가상 서버를 제공하는 서비스로 서버, OS, 소프트웨어 등을 세트로 사용할 수 있다.
* Amazon S3(Amazon Simple Storage Service)</br>
  오브젝트 스토리지 서비스로 웹 서버 및 파일 서버용 파일을 보관하는 스토리지로 사용할 수 있다.</br>
  파일 크기는 최대 5TB이며 전체 용량 제한은 없다.
* Amazon VPN</br>
  AWS 계정 전용의 가상 네트워크 환경을 구축할 수 있다.
* Amazon RDS</br>
  Amazon Auora, PostgreSQL, MySQL, MariaDB, Oracle Database, SQL Server를 이용할 수 있는 서비스이다.
* Amazon Route 53</br>
  DNS 기능을 제공한다.
* Elastic IP 주소</br>
  정적 공인 IP 주소를 제공한다.
* Amazon Managed Blockchain</br>
  블록체인 네트워크를 생성, 관리할 수 있는 도구이다.
* Amazon SageMaker</br>
  머신 러닝 모델을 구축, 트레이닝, 배포할 수 있다.</br>
  주피터 노트북(Jupyter Notebook)을 제공한다.
* AWS Cloud9</br>
  웹에서 사용 가능한 통합 개발 도구이다.
* Amazon GameLift</br>
  게임 호스팅 서비스이다.
* CodeStar</br>
  설명을 보면 CodePipeline과 비슷해 보이지만 제일 큰 차이점은 CodeStar은 아예 프로젝트 개발부터 지원해주는 서비스로, </br>
  개발을 하기 위한 프로젝트 생성 및 해당 프로젝트 리포지토리 연결, ec2 연결 등 기능을 제공한다.</br>
  또한 해당 프로젝트에 참가하는 사용자 권한 또한 관리할 수 있다.</br>
* Amazon VPC</br>
  EC2, RDS, ELB 등을 사용하는데 필요한 가상 네트워크이다.</br>
  S3나 DynamoDB 등은 VPC 외부에 있는 서비스이다.
</br></br>
## 1.3 AWS의 비용: 초기 비용은 저렴하지만 운영 비용이 조금 비싸다
<i>AWS 기봄 요금 체계 = 사용한 서비스 1개당 금액 + 사용한 분</i></br>
AWS 요금 계산기: https://calculator.s3.amazonaws.com/index.html</br>
AWS 12개월 무료 체험 서비스: https://aws.amazon.com/ko/free
</br></br>
## 1.4 AWS의 사용 방법: 누구라도 쉽게 서비스를 이용할 수 있다
매니지드 서비스는 AWS가 관리하는 서비스의 통칭으로 모든 서비스가 매니지드 서비스인 것은 아니다.</br>
매니지드 서비스는 백업 및 업데이트가 자동으로 이루어진다.
</br></br>
## 1.5 AWS의 도입 사례: 대기업이나 정부기관에서 도입하는 경우도 다수
AWS 한국 사례: https://aws.amazon.com/ko/blogs/korea/now-available-aws-korean-customer-cases/
</br></br>
## 1.6 AWS 도입 방법: 계정을 만들고 로그인하면 된다
AWS의 변화를 발표하는 AWS re:Invent: https://reinvent.awsevents.com/
</br></br></br></br>
