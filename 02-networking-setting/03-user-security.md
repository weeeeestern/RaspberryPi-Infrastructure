# 사용자 세션 분리 및 접근 제어

Raspberry Pi에서 여러 사용자 환경을 구성하고,  
서버 운영 시 필요한 계정 관리 및 보안 설정을 정리했다.

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

맨 아래에 다음 줄 추가
```
AllowUsers 사용자이름
예시 : AllowUsers alex0
```
저장 후 SSH 재시작
```bash
sudo systemctl restart ssh
```

> 설정하지 않으면 모든 계정이 SSH 접속 가능 상태가 된다.

<br>

## 3. SSH 로그인 실패 감지

SSH 접속 시도 횟수가 많을 경우 자동 차단하도록 `fail2ban`을 설치해준다

```bash
sudo apt install fail2ban -y
```

상태를 확인하고 싶다면
```bash
sudo fail2ban-client status
```

세부적인 설정을 하고 싶다면 `/etc/fail2ban/jail.conf`에서 설정이 가능하다.

<br>

## 4. 사용자 홈 디렉토리 권한 보안 설정

다중 사용자 환경에서는 각 사용자의 홈 디렉토리에 타 사용자가 접근하지 못하도록 제한해야 한다.
이를 방지하기 위해 각 홈 디렉토리의 권한을 따로 줘야한다.  
```bash
sudo chown 사용자이름:사용자이름 /home/사용자이름
sudo chmod 700 /home/사용자이름
```


