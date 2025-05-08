# 사용자 계정 보안 및 VNC 사용자 분리 설정

Raspberry Pi에서 여러 사용자 환경을 구성하고,  
서버 운영 시 필요한 계정 보안 관리와 VNC 사용자 세션 분리를 설정을 정리했다.

<br>

## 1. 사용자 계정 추가 및 권한 설정

### 사용자 추가
```bash
sudo adduser 사용자이름
예시 : sudo adduser alex2
```

### 사용자 목록 확인
```bash
cut -d: -f1 /etc/passwd

등록된 사용자만 보고싶다면
awk -F: '$3 >= 1000 && $3 < 65534 { print $1 }' /etc/passwd
```

### 현재 로그인 사용자 확인
```bash
who
```


### 관리자 권한(SUDO) 부여
```bash
sudo usermod -aG sudo 사용자이름
예시 : sudo usermod -aG sudo alex2
```


### 사용자 삭제
```bash
sudo deluser 사용자이름
sudo deluser --remove-home 사용자이름  # 홈 디렉토리도 삭제
```

### 비밀번호 변경
```bash
sudo passwd 사용자이름
예시 : sudo passwd alex2
```

<br>

## 2. SSH 접속 허용 사용자 제한

특정 사용자만 SSH 접속을 허용하고 싶다면 `/etc/ssh/sshd_config` 파일을 수정하면 된다.

```bash
sudo nano /etc/ssh/sshd_config
```

파일 하단에 아래와 같이 추가:
```
AllowUsers pi 사용자이름
예시 : AllowUsers pi alex0
```

변경 적용:
```bash
sudo systemctl restart ssh
```

> 설정하지 않으면 모든 계정이 SSH 접속 가능 상태가 된다.

<br>

## 3. 접속 실패 방지

SSH 접속 시도 횟수가 많을 경우 자동 차단하도록 `fail2ban`을 설치해준다

```bash
sudo apt install fail2ban -y
```

설치 후 기본 설정만으로도 충분히 방어 가능하며, `/etc/fail2ban/jail.conf`에서 세부 설정도 가능하다.

<br>

## 4. 사용자 홈 디렉토리 권한 보안 설정

다중 사용자 환경에서는 각 사용자의 홈 디렉토리에 타 사용자가 접근하지 못하도록 제한해야 한다.
이를 방지하기 위해 각 홈 디렉토리의 권한을 따로 줘야한다.  
```bash
sudo chown 사용자이름:사용자이름 /home/사용자이름
sudo chmod 700 /home/사용자이름
```


