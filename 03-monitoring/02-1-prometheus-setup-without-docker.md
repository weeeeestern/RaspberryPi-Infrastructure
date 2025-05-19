# [without-docker] Prometheus 설치 및 실행

우선 라즈베리파이 5의 아키텍처를 알아보기 위해
`uname -m` 명령어를 사용하여 확인해준다.

```bash
uname -m
```  
<br>

<img src="https://github.com/user-attachments/assets/60ce8e22-6fbf-443c-b76d-d48adad5abe1" width=600>

출력 결과는 `aarch64`이다.  
64비트 ARM 아키텍처이므로 이에 맞는 버전을 깔아주면 된다.  

Prometheus 공식 사이트에서 여러 버전의 OS와 아키텍처를 찾아볼 수 있다.  

<img src="https://github.com/user-attachments/assets/15fa4103-92f3-474e-8408-7b351172d5e0" width=500>  
  
<br>

2.53.4 버전과 3.4.0 최신 버전 중에 하나를 고르면 된다.  
우리는 안정성과 지원 문서를 고려해서 LTS 버전인 2.53.4를 사용하였다.   
<br>

---
<br>

### Prometheus 다운로드 & 압축해제
```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.53.4/prometheus-2.53.4.linux-arm64.tar.gz
tar -zvxf prometheus-2.53.4.linux-arm64.tar.gz
```

압축을 풀면 prometheus-2.53.4.linux-arm64 디렉토리가 생성된다.    

파일명을 바꾸고 싶다면 `sudo cp -r` 로 디렉토리 복사 후, `sudo rm -rf p` 으로 원래 파일을 삭제해주면 된다.  

<br>

### Prometheus 실행  
```bash
cd prometheus-2.53.4.linux-arm64/
./prometheus --config.file=prometheus.yml
```

명령을 실행하면 설정 파일을 기반으로 Prometheus 서버가 돌아간다.  

![image](https://github.com/user-attachments/assets/321ff19b-d84a-497b-8598-e588bf9eac77)

실행이 제대로 되었는지는 출력 로그에서 다음과 같은 메시지를 찾아주면 된다.  

```
msg="Server is ready to receive web requests."
```

또한 다음과 같은 메시지도 함께 출력된다:

```
msg="Start listening for connections" address=0.0.0.0:9090
```

이는 Prometheus가 9090번 포트에서 HTTP 요청을 받을 준비가 되었다는 뜻이다.
따라서 브라우저에 다음 주소를 쳐보면,  
<br>

<img src = "https://github.com/user-attachments/assets/1eae7dc7-d351-4f2b-ae13-7fcfc57294db" width=550>   

> `http://localhost:9090` 또는 `http://127.0.0.1:9090`으로 접속 가능하다.

이렇게 Prometheus 웹에서 현재 수집 중인 메트릭, 타임라인 그래프, 쿼리 등을 실시간으로 확인할 수 있다.  

<br>

### Prometheus 자동 실행
위에서 했듯이, Prometheus는 단독 실행도 가능하지만, 재부팅 마다 수동으로 시작해야하는 단점이 있다.  

이를 해결하기 위해서 Prometheus를 systemd 서비스로 등록하면 시스템 시작 시 자동으로 실행되도록 설정할 수 있다.  
<br>

**1. systemd 서비스 파일 생성**  

먼저 Prometheus 실행 정보를 담아둔 서비스 파일을 만들어준다.   
```bash
sudo nano /etc/systemd/system/prometheus.service
```

(경로는 실제 Prometheus 설치 경로에 맞게 수정!) 

```ini
[Unit]
Description=Prometheus Server
Wants=network-online.target
After=network-online.target

[Service]
User=root
Group=root
Type=simple
ExecStart=/home/alex0/prometheus-2.53.4.linux-arm64/prometheus \
  --config.file=/home/alex0/prometheus-2.53.4.linux-arm64/prometheus.yml

[Install]
WantedBy=multi-user.target
```

* `User`, `Group`: Prometheus를 어떤 사용자 권한으로 실행할지 설정한다. 일반적으로 `root` 또는 별도 사용자 사용 가능.
* `ExecStart`: Prometheus의 실행 경로와 설정 파일 경로를 명시.
* `After`, `Wants`: 네트워크가 활성화된 이후에 실행되도록 지정함.  
<br>

**2. 서비스 등록 및 실행**  

서비스 파일을 저장한 후 systemd에 반영하고 실행할 수 있도록 해줘야한다.  

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
```

* `daemon-reexec`와 `daemon-reload`는 새로운 서비스 파일을 systemd에 적용하기 위한 명령.
* `enable`은 시스템 부팅 시 자동으로 시작되도록 설정.
* `start`는 Prometheus 서비스 즉시 시작.  
<br>

**3. 실행 상태 확인**    

서비스가 잘 실행되는지 확인하는 명령어이다.  

```bash
sudo systemctl status prometheus
```

<img src="https://github.com/user-attachments/assets/4b2d002e-efbd-490c-85e2-06d9db43e8e4" width=550>    

정상적으로 실행되었다면 "active (running)" 상태가 표시된다.
