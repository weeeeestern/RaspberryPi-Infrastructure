# Raspberry Pi 5 OS 설치 가이드 (SSD 부팅)

## 🛠️ 준비물
- **라즈베리파이5 8GB**
- **라즈베리파이5 전용 쿨러**
- **라즈베리파이5 전용 어댑터 (5V 5A지원 27W)**
- **SSD 2242 M.2 NVMe 256GB**
- **M.2 NVMe SSD USB 3.2 GEN2 10Gbps 외장하드 케이스**
- **Micro HDMI to HDMI 2.0**


<br>

## 1️⃣ **라즈베리 파이 OS 설치 (SSD에 직접 설치)**
1. **SSD를 PC에 연결**  
   - NVMe SSD를 USB 리더기에 장착하고 PC에 연결

2. **라즈베리 파이 이미저 다운로드 및 실행**  
   - [공식 사이트](https://www.raspberrypi.com/software/)에서 다운로드  
   - "라즈베리 파이 5" 선택  
   - "64비트 OS" 선택 후 설치 진행

3. **설치 완료 후 PC에서 SSD 분리 및 다시 연결**  
   - OS 설치가 완료되면 SSD를 분리 후 다시 연결  

4. **PCIe 설정 수정 (config.txt 변경)**
   - SSD에서 `config.txt` 파일을 열고 **하단에 다음을 추가**
   ```ini
   # PCIe 설정을 활성화합니다.
   [all]
   dtparam=pciex1  # PCIe 장치를 인식할 수 있도록 활성화 (M.2 NVMe SSD 사용 가능)
   dtparam=pciex1_gen3=3  # PCIe 3.0 모드에서 작동하도록 설정

   # PCIe (Peripheral Component Interconnect Express)
   # 고속 데이터 전송을 위한 컴퓨터 확장 슬롯 인터페이스
   ```

5. **부팅 순서 변경 (MicroSD → SSD)**  
   - MicroSD가 아닌 SSD에서 부팅을 하므로 내부 EEPROM에서 부팅 순서를 변경해야 함  
   - 부팅 후 터미널에서 EEPROM 설정 수정  
   ```bash
    sudo rpi-eeprom-config --edit
   ```
   -  `BOOT_ORDER` 값을 아래와 같이 변경:  
   ```ini
    BOOT_ORDER=0xf41  # 기존 값
    BOOT_ORDER=0xf416  # 변경 후 (SSD 부팅 활성화)
   ```  
   - **PCIe 장치 탐색 활성화**
   ```ini
    PCIE_PROBE=1
   ```
  
   - 변경 후 저장: `Ctrl + X` → `Y` → `Enter`  
