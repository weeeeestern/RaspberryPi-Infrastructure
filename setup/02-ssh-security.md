# SSH 접속 가이드 (PuTTY 사용) 🔑

라즈베리파이 5가 정상적으로 부팅되고, **같은 네트워크 대역**에 연결되어 있다면 **SSH**를 통해 원격으로 접속 가능.  
<br>
![스크린샷_2025-03-19_152746_resized](https://github.com/user-attachments/assets/ffbaf5e8-fec1-446a-adb0-85b96afa1a24)


## 1️⃣ Host Name 및 포트 설정
- **Host Name (or IP address)**: `raspberrypi`  
  → 전 단계에서 `raspberrypi.local`로 설정해놨으므로 raspberrypi를 입력
- **Port**: `22`  
  → SSH 기본 포트는 `22`이며, 특별히 변경하지 않았다면 기본값 그대로 사용  

## 2️⃣ Connection Type 설정
- **SSH** 옵션 선택 (기본적으로 SSH를 통한 접속이므로 필수)  


## 3️⃣ 기타 설정
- **Close window on exit**  
  - `Only on clean exit` → 정상 종료 시만 창을 닫음  
  - `Always` → 접속 종료 시 무조건 창이 닫힘  
  - `Never` → 접속 종료 후에도 창이 유지됨  

## 4️⃣ 최종 접속
- 설정이 완료되면 **Open** 버튼을 클릭.


<br><br>

---

### PuTTY 보안 경고 (PuTTY Security Alert) 🚨

라즈베리파이에 처음 SSH로 접속할 경우 **PuTTY Security Alert** 경고창이 나타남.  
라즈베리파이의 보안 키가 PuTTY에 아직 등록되지 않았다는 의미.  

### 버튼 설명
- **[Accept]**  
  → 이 버튼을 누르면 해당 보안 키가 PuTTY에 저장됨.  
  → 이후 동일한 라즈베리파이에 접속할 때 더 이상 경고가 표시되지 않음.  
  → **앞으로 계속 연결할 것이므로 이 옵션을 선택**.  

- **[Connect Once]**  
  → 보안 키를 저장하지 않고 한 번만 연결을 허용.  
  → 다음에 다시 접속할 경우 동일한 경고가 나타남.  

---
<br>

## 1️⃣ SSH 로그인 화면  
![스크린샷 2025-03-19 150839_resized](https://github.com/user-attachments/assets/239e8bd2-0f61-4037-b4c3-45dbcf73d3d1)

- **이전에 설정한 사용자 이름과 비밀번호**를 입력.  
- **비밀번호 입력 인터페이스는 원래 표시가 안됨.** 비밀번호 입력 시 화면에 보이지 않지만, 정상적으로 입력되고 있으니 그대로 입력 후 `Enter`를 누르면 된다.  

## 2️⃣ VNC 설정 활성화
```bash
sudo raspi-config
```
- VNC는 원격 데스크톱 접속을 위한 기능이므로, 원격으로 GUI 환경에서 라즈베리파이를 제어하려면 반드시 활성화해야 한다.


![스크린샷 2025-03-19 151632_resized](https://github.com/user-attachments/assets/e4e068d4-adfe-4a3f-b45b-ca95d063be91)

![스크린샷 2025-03-19 151642_resized (1)](https://github.com/user-attachments/assets/8a731501-eac2-46a5-803e-2f74156420eb)

- 설정 화면에서 Interface Options → VNC 항목으로 이동하여 Enable (활성화)로 설정한다.  
- 설정이 끝났으면 원격접속 준비가 끝남.  









