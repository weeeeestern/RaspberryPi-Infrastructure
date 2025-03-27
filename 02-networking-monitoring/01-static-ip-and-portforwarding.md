# 고정 IP 할당 & 포트포워딩 설정

라즈베리파이에 외부에서 접속하기 위해서는 **고정 IP를 할당**하고, **포트포워딩 설정**해야함.

<br>

### 고정ip가 필요한 이유
- 공유기에서는 보통 **DHCP(동적 IP 할당)** 방식으로 내부 IP를 자동할당
- 자동할당을 받으면 재부팅하거나 시간이 지나면 **ip가 바뀔 수 있음**


 **DHCP 수동 설정 (Static DHCP)** 을 통해 **고정 ip를 할당** 해줘야 포트포워딩에 문제가 생기지 않음

<img src="https://github.com/user-attachments/assets/311c93b9-963d-4406-a529-0d3401559b85" width="600"/>

이해를 돕기위한 사진

<br>

## 1️⃣ DHCP 고정 할당하는 방법


<img src="https://github.com/user-attachments/assets/8c66d76f-b3f0-464d-a766-fd8b1c83edb3" width="600"/>

1. 공유기 관리자 페이지 접속 후   
2. DHCP 할당정보 페이지 찾기  
   - 로그인 후 **DHCP 할당정보 → raspberrypi MAC주소** 찾기(하드웨어 주소랑 같은거임)  
  
<br>
<br>
<img src="https://github.com/user-attachments/assets/c43bc8b6-0a22-4c44-8feb-301ece74a95e" width="600"/>


3. MAC 주소 기반으로 고정 IP  등록    
   - 예: MAC 주소 `B8:27:EB:XX:XX:XX` → IP: `192.168.219.100`      

<br>

## 2️⃣ 포트포워딩 설정

<img src="https://github.com/user-attachments/assets/aef3e568-9788-4285-ab63-1201b085ca69" width="400"/>

### SSH 접속 (22번 포트)
- 외부 포트: `22`
- 내부 IP: `192.168.219.100`
- 내부 포트: `22`
- 프로토콜: TCP

<img src="https://github.com/user-attachments/assets/7430ab73-7267-4cac-bc39-f83588a6175e" width="400"/>

<br>

### VNC 접속 (5900 포트)
- 외부 포트: `5900`
- 내부 포트: `5900`
- 프로토콜: TCP

### 최종
<img src="https://github.com/user-attachments/assets/e2cd2e63-3931-4517-99fc-89f410fa0b94" width="700"/>

<br>
<br>

## 3️⃣ CGNAT 주의사항

**CGNAT (Carrier-Grade NAT)** 환경에서는 외부에서 내부 네트워크로 직접 접근이 불가능함.   
(ISP가 공인 IP를 여러 사용자에게 공유해서 발생하는 구조.)

### CGNAT 여부 확인 방법
- [https://whatismyipaddress.com/](https://whatismyipaddress.com/) 로 확인한 **공인 IP**가 공유기 설정 페이지에 보이는 WAN IP와 다르면 CGNAT일 가능성이 큼.

> 참고) 대부분의 가정용 광랜/KT/SK브로드밴드/LG U+ 등에서는 **CGNAT을 사용하지 않음**.  
> 일부 모바일 핫스팟, LTE 유심, 기업형 인터넷 회선에서만 CGNAT이 적용됨.


<br>

**2번까지 마무리를 했다면 외부에서 접속 준비 끝!**


