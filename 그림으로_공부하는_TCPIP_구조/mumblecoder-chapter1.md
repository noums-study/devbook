```
이 장은 네트워크의 정의, 규칙, 기기, 형태에 대해서 간략하게 훑어본다.
```

## Chapter 1 네트워크 기초

### 1.1 네트워크란
- 회선 교환 방식은 데이터를 교환하기 전에 일대일의 전송로를 만들고, 교환을 마칠때까지 전송로를 계속 사용하는 방식
- 회선 이용 효율을 향상할 목적으로 새롭게 고안된 것이 패킷 교환 방식
- 패킷 교환 방식은 데이터를 패킷이라는 작은 단위로 나누어 네트워크로 보내는 방식
- 헤더에는 수신 컴퓨터 정보, 데이터 중 몇 번째에 해당하는 패킷인지에 관한 정보 등 다양한 정보가 포함되어 있다.
</br>
 
### 1.2 통신을 위한 규칙, 프로토콜
- 네트워크 세계에서는 패킷을 처리하기 위한 규칙이 존재
- LAN 케이블 소재나 커넥터 형태, 그 핀 할당에 이르기까지 네트워크에서 눈에 보이는 것들은 모두 프로토콜에 정의되어 있다.
- 프로토콜에는 헤더의 어디에서 어디까지 어떤 정보를 포함하고 어떤 순서로 교환하는지 등이 정의되어 있다.
- 프로토콜은 에러를 알리거나 데이터를 재전송하는 구조를 제공한다. 또한, 유한한 네트워크 자원이 패킷으로 가득 차서 잠기지 않도록 하기 위한 구조도 제공
- 프로토콜은 중요한 정보를 안심하고 교환할 수 있도록 올바른 통신 상대인지 인증하고 통신을 암호화하는 구조를 제공
- 프로토콜로 정의된 다양한 통신 기능은 그 처리에 맞게 계층 구조로 되어 있다. (TCP/IP 참조 모델, OSI 참조 모델 참고)
- 계층에서 처리하는 한 덩어리의 데이터 단위는 PDU(Protocol Data Unit)이라 부른다.
- PDU는 제어 정보를 포함한 헤더, 데이터 자체인 페이로드로 구성되어 있으며, 처리되는 계층에 따라 명칭이 다르다.
- 송신 단말은 애플리케이션 계층에서 순서대로 각 계층에서 페이로드에 헤더를 붙여 PDU로 만들어 바로 아래 계층으로 전달한다. (캡슐화)
- 대응하는 수신 단말은 물리 계층에서 순서대로, 각 계층에서 PDU로부터 헤더를 제거하고 페이로드만을 바로 위 계층으로 전달한다. (비캡슐화)
</br>

### 1.3 네트워크 구성 기기
- 모든 네트워크 기기가 모든 계층의 프로토콜 정보를 처리할 수 없다. 기기 종류에 따라 처리 범위가 다르다.
- 물리 계층은 케이블이나 커넥터 형태, 핀 할당등 물리적인 사양에 관해 모두 정의되어 있는 계층
- 물리 계층에서 동작하는 기기로는 NIC, 리피터, 리피터 허브, 미디어 컨버터, 액세스 포인트등이 있다.
- 데이터링크 계층은 물리 계층의 신뢰성을 확보하고, 같은 네트워크에 있는 단말과 연결할 수 있도록 하는 계층
- 데이터링크 계층에서 동작하는 기기로는 브릿지, L2 스위치가 있다. 기본 기능은 단말에서 받아들인 프레임의 MAC 주소를 MAC 주소 테이블로 관리하고, 전송 처리이다.
- 네트워크 계층은 네트워크와 네트워크를 연결하는 계층. 동작하는 기기로는 라우터, L3 스위치(라우터+L2 스위치)가 있다.
- 트랜스포트 계층은 애플리케이션을 식별하고, 그 요건에 맞게 통신 제어하는 계층, 동작하는 기기로는 방화벽이 있다.
- 방화벽은 단말 사이에서 교환되는 패킷의 IP 주소나 포트 번호를 보고, 통신을 허가하거나 차단한다.
- 애플리케이션 계층은 사용자에게 애플리케이션을 제공하는 계층, 동작하는 기기로는 차세대 방화벽, WAF, L7 스위치(=로드밸런서)가 있다.
</br>

### 1.4 다양한 네트워크 기기 형태
- 물리 어플라이언스는 눈에 보이는, 다시 말해 '상자 모양의 장치'이다.
- 가상 어플라이언스는 가상화 기술을 제공하는 소프트웨어 위에서 동작하는 네트워크 기기이다.
</br>

### 1.5 네트워크 형태
- LAN은 'Local Area Network'의 약자로, 가정이나 기업 등 한정된 범위의 네트워크를 의미한다.
- WAN은 'Wide Area Network'의 약자로 거리상 멀리 떨어진 범위의 네트워크를 의미한다.
- 인터넷은 WAN으로 분류된다. 인터넷은 간단히 말하면 라우터의 집합이다.
- 폐역 VPN망은 LAN과 LAN을 연결하는 네트워크를 의미한다.
- DMZ는 'DeMilitarized Zone'의 약자로, 인터넷에 공개하는 서버를 설치한 네트워크를 의미한다.
- DMZ에서는 어떤 기기가 고장나더라도, 어떤 케이블이 끊어지더라도 경로가 즉시 전환되어 서비스를 계속 제공할 수 있도록 동일한 종류의 네트워크 기기를 병렬로 배치한다.
</br>

### 1.6 새로운 네트워크 형태
- SDN은 'Software Defined Network'의 약자로 소프트웨어에 의해 관리 및 제어되는 가상적 네트워크, 또는 이를 구성하기 위한 기술을 의미한다.
- SDN은 오버레이 타입과 홉바이홉으로 나뉜다. 오버레이 타입은 스위치 사이에 가상 터널을 만들고, 패킷을 전송하는 방식이고, 홉바이홉 타입은 각 스위치에 경로 정보를 배포하고, 그 정보를 기반으로 패킷을 전송하는 방식.
- 최근 홉바이홉 타입이 쇠퇴함에 따라 SDN이라 하면 대부분 오버레이 타입이다.
- CDN은 'Content Delivery Network'의 약자로 이미지, 동영상, HTML, CSS 등 웹콘텐츠로 사용되는 다양한 파일을 대량으로 송신하기 위해 최적화된 인터넷상의 서버 네트워크를 의미한다.
- CDN은 오리지널 컨텐츠를 가진 오리진 서버와 그 캐시를 갖는 엣지 서버로 구성된다. 사용자는 물리적으로 가까운 엣지 서버에 접속한다.
- 엣지 서버는 캐시를 갖고 있지 않거나, 그 유효 기간이 만료되었을 때만 오리진 서버에 접속한다.
- IoT는 'Internet of Things'의 약자로, 주변에 있는 다양한 것들이 인터넷에 연결된 구조를 의미한다.
- IaaS는 'Infrastructure as a Service'의 약자로 서버나 네트워크 등의 인프라를, 인터넷상에서 구축하는 클라우드 서비스의 하나이다.

