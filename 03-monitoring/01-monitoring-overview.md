# 모니터링 구성

원격제어를 할 수 있는 환경을 만들었으므로,   
이제 Prometheus 와 Granfana 를 이용하여 시스템 모니터링을 구축해볼 예정이다.  

본격적인 실습에 들어가기 전에 각 서비스의 역할과 사용할 모니터링 아키텍처 구조를 알아보자.  

<br>  

## Prometheus
Prometheus는 시스템의 상태나 성능을 주기적으로 수집하여 저장하고, 이를 분석할 수 있도록 해주는 오픈소스 모니터링 시스템이다.    
<br>

### Prometheus 구조  
![스크린샷 2025-05-10 010238](https://github.com/user-attachments/assets/c4cd74eb-b9a5-4c03-8e3b-1242c3f4e020)


Prometheus 는 일반적인 모니터링 시스템과 데이터 수집 방식과 차이점이 있다.  
<br>

**일반적인 모니터링 구조**  
- 일반적인 모니터링 구조로는 중앙 모니터링 서버가 각 대상 서버에 push 요청이나 agent 실행으로 상태 정보를 받는다.  
- 혹은 각 대상 서버가 중앙 서버에 주기적으로 데이터를 push 한다.  

**Prometheus 모니터링 구조**  
- Promethrus 는 자기가 주도적으로 각 Exporter에 접속해서 메트릭을 수집한다.
- 대상 서버에 Exporter 만 실행되면 되고, Prometheus가 직접 HTTP 요청을 보내 데이터를 받아간다.  

<br>

<img src="https://github.com/user-attachments/assets/12a58721-fffd-412c-b570-5dcc00e2b0e5" style="width: 50%;" alt="차이점 사진">



Prometheus는 중앙 서버가 직접 지표(Matric) 수집하는 Pull 중심 구조라는 점에서 기존 모니터링 시스템과 확실히 다르다.   
<br>

**Prometheus 가 Pull 방식을 채택한 이유?**  
이러한 방식은 운영 측면에서 확실한 이점이 있기 때문이다.  
대상 서버에서 중앙 서버로 메트릭을 보내는 push 방식은, 대상 서버의 리소스 상황과 무관하게 데이터를 전송해야한다.  
이는 특정 장애 상황에서 오히려 추가적인 네트워크 작업이 문제를 악화시킬 수 있다.  

반면 Prometheus는 중앙 서버가 각 대상 서버의 Exporter에게 직접 접속하여 지표(Matric)을 가져오는 pull 방식이기 때문에, 대상 서버는 지표(Matric)을 준비해두고 요청이 올 때만 응답하면 된다.  
즉, 시스템의 무리를 줄일 수 있는 방식이다.  
<br>

**추가 특징**  
이러한 구조적 이점 외에도 Prometheus는   
단순한 설정, Exporter와의 직관적인 연동, Granfana 와 같은 시각화 도구와 쉬운 연동 등 많은 이점이 있다.  
덕분에 초기 진입 장벽이 낮고 동적 인프라 환경에서도 유연하게 적용할 수 있다.  

물론 단점도 존재한다.  
기본적으로 Prometheus는 단일 서버 구성이라 분산 수집이나 고가용성 운영에는 한계가 있다.  
장기 보존이 필요한 경우에는 외부 스토리지 연동이 필요하고,  
사용자 인증이나 암호화 같은 보안 기능도 기본 제공되지 않는다.  

그럼에도 불구하고, Prometheus는 단순함, 안정성, 확장성 모두 균형있게 갖춘 설계 덕에 가장 널리 쓰이는 오픈소스 모니터링 도구이다.   

<br>  

## Granfana

![스크린샷 2025-05-10 005223](https://github.com/user-attachments/assets/fc4945ec-f0e5-41cc-a090-bb42b1eaaed5)

Grafana는 모니터링 데이터를 시각적으로 보여주는 도구이다.  

CPU 사용률, 메모리 상태, 데이터베이스 연결 수 같은 값들을 그냥 숫자만 보면 파악하기 어렵기 때문에,   
Grafana를 통해 숫자들을 그래프나 테이블로 바꿔서 한눈에 시스템 상태를 확인할 수 있게 해준다.   

웹 UI를 통해 직접 대시보드를 구성할 수 있으며, 필요한 시간 범위를 선택하거나 특정 조건을 기준으로 색상을 다르게 표시할 수 있는 등 여러 시각화 기능을 지원한다.   

<br>
<br>

## 실습 모니터링 아키텍처 구조
![아키텍처구조](https://github.com/user-attachments/assets/ccdb577d-1624-4c26-89a7-aaa22e30d0ae)


이번 실습 모니터링 시스템의 전체 구성도이다.  
이 구성은 Prometheus와 Grafana를 중심으로, 각 데이터베이스 서버에 설치된 Exporter들로부터 메트릭을 수집하고 시각화하는 구조를 따른다.  

<br>

### 1. Monitoring Server  
Grafana로 Prometheus를 데이터 소스로 참조하여 메트릭을 불러와서 웹 인터페이스로 시스템 상태를 모니터링한다.  

Prometheus는 메트릭 수집의 중심 역할을 하며, 각 대상 서버에 설치된 Exporter에게 주기적으로 Pull 방식의 요청을 보내 메트릭 데이터를 수집한다.   

<br>

### 2. 대상 서버 (MySQL Server 1, 2)  
각 DB 서버에는 두 가지 Exporter가 설치되어 있다  
- **Node Exporter**: 서버의 CPU, 메모리, 디스크, 네트워크 등 시스템 수준의 메트릭을 수집한다.
- **MySQL Exporter**: MySQL 인스턴스로부터 쿼리 수, 연결 수, 슬로우 쿼리 비율 등 데이터베이스 관련 메트릭을 수집한다.

이 Exporter들은 수동적으로 대기하며, Prometheus가 요청을 보낼 때 `/metrics` 엔드포인트로 메트릭 정보를 응답한다.   

<br>

### 3. 전체 흐름
- Prometheus는 각 대상 서버의 Exporter(Node, MySQL)에 주기적으로 Pull 요청을 보낸다.
- Exporter는 준비된 메트릭 데이터를 응답한다.
- 수집된 데이터는 Prometheus 내부 시계열 DB에 저장된다.
- Grafana는 Prometheus에 쿼리를 날려 데이터를 시각화하고, 사용자는 웹 UI를 통해 실시간으로 확인할 수 있다.

