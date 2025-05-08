# 방화벽 설정

### 1. UFW 설치
우선 방화벽을 사용하기 위해, 방화벽을 다운 받아준다.   
```bash
sudo apt update
sudo apt install ufw
```
<br>

### 2. SSH & VNC 접속 포트 허용 (선택 사항)
ssh와 vnc viewer를 사용하기 위해 22 포트와 5900 포트를 열어준다.  
```bash
sudo ufw allow 22/tcp     # SSH 포트
sudo ufw allow 5900/tcp   # VNC 포트
```
<br>

### 2-1. 특정 IP에서만 접근 허용  
만약 특정 IP에서만 접근 가능하게 하고 싶다면 이렇게 설정하면 된다.  
고정ip가 아니라면 특정 대역으로 설정해주자.  
```bash
sudo ufw allow from [내 IP 주소] to any port 5900 proto tcp

예시 : sudo ufw allow from 192.168.0.0/24 to any port 5900 proto tcp
```

### 3. 방화벽 실행    
SSH/VNC 포트를 허용하지 않은 상태에서 실행하면 원격 연결이 끊길 수 있으니 주의해야 한다.  
```bash
sudo ufw enable
```
<br>

### 4. 방화벽 상태 확인  

```bash
sudo ufw status verbose
```
<br>

![image](https://github.com/user-attachments/assets/6fcdbe69-5990-45f9-a92a-0dfd5994d1ce)

실행해보면 ipv4와 ipv6 각각 허용되어 있는 것을 볼 수 있다.  


