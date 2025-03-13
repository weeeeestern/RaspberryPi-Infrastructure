# Raspberry Pi 5 OS 설치 가이드 (SSD 부팅)

## 🛠️ 준비물
- **라즈베리파이5 8GB**
- **라즈베리파이5 전용 쿨러**
- **라즈베리파이5 전용 어댑터 (5V 5A지원 27W)**
- **SSD 2242 M.2 NVMe 256GB**
- **M.2 NVMe SSD USB 3.2 GEN2 10Gbps 외장하드 케이스**
- **Micro HDMI to HDMI 2.0**
<br>

---

<br>


## 1️⃣ **라즈베리 파이 OS 설치 (SSD에 직접 설치)**

### - **라즈베리 파이 OS 설치 준비**
-  [라즈베리파이 이미저 다운로드](https://www.raspberrypi.com/software/) 후 실행
-  NVMe SSD를 USB 리더기에 장착하고 PC에 연결
<br>

**설치 단계**
- `Choose Device` → **라즈베리 파이 5**
- `Choose OS` → **Other general-purpose OS** → **Ubuntu** → **Ubuntu Desktop 24.10 (64-bit)**
<br>

**추가 설정 화면**

![KakaoTalk_20250313_132545867_resized](https://github.com/user-attachments/assets/9e2c3968-5775-4e2c-995a-f61589882a3b)
![image_resized](https://github.com/user-attachments/assets/ee1f0ce0-3223-42bd-ae6c-21c7d4fec0fe)

- **Ctrl + Shift + X** 키를 눌러 커스터마이징 화면을 활성화
- 다음 설정항목을 채우기
  - `사용자 이름 및 비밀번호 설정` → 사용자 이름, 비밀번호 설정
  - `무선 LAN설정` → Wifi 아이디 및 비밀번호 입력
  - `무선 LAN 국가 설정` → KR
  - `로캐일 설정` → 시간대: `Asia/Seoul`, 키보드 레이아웃: `us`
  - `SSH 활성화` → 체크  


**설치 진행**
- 선택 완료 후 설치 진행
<br>

### - **설치 후 연결 및 부팅 과정**

1. **SSD 카드 연결**
   - 노트북에 연결된 **USB 리더기 (C to USB 케이블)** 분리
   - 분리 후 **라즈베리파이 5의 USB 3.0 포트 (파란색)** 에 연결

2. **전원 및 디스플레이 연결**
   - **라즈베리파이 전용 어댑터**를 전원 포트에 연결
   - **HDMI to Micro HDMI** 케이블을 사용해 **라즈베리파이 5**와 **노트북**을 연결

3. **부팅 확인**
   - 전원을 연결하면 라즈베리파이 5의 상태 LED가 점등됨

---

### - **LED 상태 표시 (부팅 상태 확인)**

| **LED 색상** | **LED 상태** | **의미** |
|:--------------|:--------------|:------------|
| 🟢 **녹색 LED** | **깜빡임** | 정상적으로 부팅 중 |
| 🔴 **빨간색 LED** | **상시 점등** | 전원 공급 정상 |
| 🟢 **녹색 LED** | **꺼짐** | 부팅 실패 (OS 인식 불가) |
| 🟢 **녹색 LED** | **빠르게 점멸** | SD 카드 또는 SSD 인식 문제 |
| 🔴 **빨간색 LED** | **깜빡임** | 전원 불안정 (어댑터 문제 가능성) |
| 🚫 **두 LED 모두 꺼짐** | **꺼짐** | 전원 공급 불량 또는 보드 문제 |
















