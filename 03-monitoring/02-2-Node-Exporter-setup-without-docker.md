# [without-docker] Node Exporter 설치 및 실행

Prometheus 공식 사이트에서 linux OS와 arm64 아키텍쳐를 지원하는 Node Exporter 버전을 찾아볼 수 있다.  

<img src="https://github.com/user-attachments/assets/820f1851-cbda-453a-9dd5-f6f0e9156467" width=500>  

<br>

1.9.1 버전이 있으므로 이 버전을 사용했다.  

<br>

---

<br>

### Node Exporter 다운로드 & 압축해제  
```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.9.1/node_exporter-1.9.1.linux-arm64.tar.gz
tar -zvxf node_exporter-1.9.1.linux-arm64.tar.gz
```

압축을 풀면 `node_exporter-1.9.1.linux-arm64` 디렉토리가 생성된다.

파일명을 바꾸고 싶다면 `sudo cp -r` 명령어를 사용해 디렉토리를 복사한 뒤,
기존 디렉토리는 `sudo rm -rf` 명령어로 삭제하면 된다.

```bash
sudo cp -r node_exporter-1.9.1.linux-arm64 node_exporter
sudo rm -rf node_exporter-1.9.1.linux-arm64
```

<br>

### Node Exporter 실행

```bash
cd node_exporter-1.9.1.linux-arm64/
./node_exporter
```  

위 명령을 실행하면 Node Exporter가 기본 설정으로 9100번 포트에서 실행된다.  

<img src ="https://github.com/user-attachments/assets/48a3132f-3768-4336-a777-cbd464e37b1f" width = 650>   

다음 메시지가 있다면 정상적으로 출력이 된 것이다.  

```
msg="Listening on" address=":9100"
```

이는 Node Exporter가 9100 포트에서 HTTP 요청을 받을 준비가 되었다는 뜻이므로 직접 쳐보면,  

<img src ="https://github.com/user-attachments/assets/c5558603-a877-49d9-95da-e1157a22c756" width = 450>    

> `http://localhost:9100/metrics` 또는 `http://0.0.0.0:9100/metrics`으로 접속 가능하다.  

페이지에 Node Exporter가 정상적으로 실행되고 있는 것을 볼 수 있다.  

내부에는 실제 Node Exporter가 수집한 메트릭들이 존재한다.  

**예시 사진**    
<img src ="https://github.com/user-attachments/assets/c42bbf3c-5e8e-4453-b8f8-064318d8c6e5" width = 350>    
<img src ="https://github.com/user-attachments/assets/41eacfa6-0e02-4bbe-8298-205521af072e" width = 550>     
<img src ="https://github.com/user-attachments/assets/fdf6837b-e98c-4b81-9f84-a2b4f4f06b29" width = 700>     

* CPU 사용량
  `node_cpu_seconds_total{mode="user"}` → 사용자 모드에서의 누적 CPU 시간

* 메모리 사용량
  `node_memory_MemAvailable_bytes` → 사용 가능한 메모리 (바이트)

* 디스크 사용량
  `node_filesystem_avail_bytes` → 남은 디스크 공간

* 네트워크 수신량
  `node_network_receive_bytes_total` → 누적 수신 바이트

* 시스템 부팅 시간
  `node_boot_time_seconds` → 부팅 시각 (UNIX timestamp)
  

이러한 메트릭들이 Prometheus에서 주기적으로 수집되는 것이다.  


<br>

### Node Exporter 자동 실행

Node Exporter도 마찬가지로 systemd를 통해 자동으로 실행되게 설정할 수 있다.   
<br>

### 1. 서비스 파일 작성  
우선, 아래 명령어로 systemd 서비스 파일을 열어준다.

```bash
sudo nano /etc/systemd/system/node_exporter.service
```  

아래 내용을 작성한다.  

```ini
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=root
Group=root
Type=simple
ExecStart=/home/alex0/node_exporter-1.9.1.linux-arm64/node_exporter

[Install]
WantedBy=multi-user.target
```

* **\[Unit]**

  * `Description`: 서비스 설명
  * `Wants` / `After`: 네트워크가 준비된 후 실행되도록 설정

* **\[Service]**

  * `User`, `Group`: Node Exporter를 실행할 시스템 사용자 지정 
(테스트를 위한 실습 서버기 때문에 root로 지정해주었지만, 실제 운영 환경에서는 별도 사용자로 분리해서 최소권한만 부여하는 것이 안전하다.)
  * `Type=simple`: 포그라운드에서 실행되는 단순 프로세스 타입
  * `ExecStart`: 실제 Node Exporter 바이너리 실행 경로

* **\[Install]**

  * `WantedBy=multi-user.target`: 일반 사용자 대상의 runlevel에서 자동 시작되도록 설정
<br>



### 2. 서비스 등록 및 실행

서비스 파일 작성 후 다음 명령어로 등록&저장을 해준다.  

```bash
sudo systemctl daemon-reload
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
```
<br>

### 3. 상태 확인  

```bash
sudo systemctl status node_exporter
```

<img src="https://github.com/user-attachments/assets/238d7f88-37a2-40b0-bc08-52e9325e6786" width=600>  

`active (running)` 상태이면 정상적으로 실행된 것이다.


이제 Node Exporter 준비도 끝났다. 수동 실행 없이 시스템 메트릭을 제공할 수 있다.
