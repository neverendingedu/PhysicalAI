이 문제는 이전 단계에서 535 드라이버 패키지는 설치되었으나, **시스템 부팅 시 우분투 커널(메모리)에 NVIDIA 드라이버 모듈(`nvidia.ko`)이 정상적으로 로드(적재)되지 않아서** 발생합니다.

가장 흔한 원인은 **Secure Boot(보안 부팅)** 기능이 켜져 있어서 우분투 커널이 새로 설치된 드라이버를 "신뢰할 수 없는 외부 모듈"로 간주하고 차단해 버렸거나, 드라이버 커널 컴파일(DKMS)이 누락되었기 때문입니다.

정확한 원인을 파악하고 해결하기 위해 터미널에서 다음 순서대로 진행해 주세요.

1. **수동으로 커널 모듈 로드 시도 및 에러 확인:** 10초.
터미널에 다음 명령어를 입력하여 강제로 드라이버 모듈을 켜보고 튕겨내는 원인을 진단합니다.

```bash
sudo modprobe nvidia

```

**이 명령어를 쳤을 때 나타나는 결과에 따라 원인과 해결책이 다릅니다:**

* **아무 메시지 없이 바로 다음 줄로 넘어간 경우:** 일시적 로드 지연이었습니다. 다시 `nvidia-smi`를 입력해 보세요. 표가 잘 나온다면 해결된 것입니다!
* **`Key was rejected by service` 또는 `Operation not permitted` 에러가 뜨는 경우:** 100% **Secure Boot에 의한 차단**입니다. (👉 아래 **2단계**로 이동하여 해결)
* **`Module nvidia not found in directory...` 에러가 뜨는 경우:** 535 드라이버 버전조차 현재 커널(6.8.0-138) 환경에서 모듈 빌드가 실패했거나 꼬인 것입니다. (👉 아래 **3단계**로 이동하여 해결)


2. **해결 방법 A: BIOS에서 Secure Boot 끄기 (가장 권장):** (권한 거부 에러 발생 시).
Secure Boot 차단이 원인일 경우, 메인보드 BIOS(UEFI)에서 보안 부팅 기능을 끄는 것이 로봇/Sim-to-Real 환경 세팅 시 가장 정신 건강에 좋습니다.

1. 시스템을 다시 시작(Reboot)합니다.
2. 컴퓨터가 켜지며 제조사 로고가 보일 때 **F2, F12, Delete, F10** 등의 키(컴퓨터 제조사마다 다름)를 연타하여 **BIOS/UEFI 설정 화면**으로 들어갑니다.
3. BIOS 메뉴에서 `Security`, `Boot`, 또는 `Authentication` 탭을 찾습니다.
4. **`Secure Boot`** 항목을 찾아서 **`Enabled`를 `Disabled`로 변경**합니다.
5. **F10** 키를 눌러 저장하고 종료(Save & Exit)하여 우분투로 다시 부팅합니다.
6. 부팅 후 터미널에서 `nvidia-smi`를 실행해 봅니다.


3. **해결 방법 B: 우분투 자동 드라이버 설치 활용:** (Module not found 에러 발생 시).
만약 535 모듈 빌드도 실패(Not found)했다면, 우분투 OS가 시스템과 커널 버전을 스캔하여 **가장 호환성이 완벽한 드라이버를 스스로 찾아서 설치**하게끔 해야 합니다.

```bash
# 1. 껍데기만 깔린 기존 드라이버 백지화
sudo apt-get purge -y "*nvidia*" "*libnvidia*"
sudo apt-get autoremove --purge -y
sudo rm -rf /var/lib/dkms/nvidia

# 2. 우분투 권장 드라이버 자동 스캔 및 설치
sudo ubuntu-drivers autoinstall

# 3. 설치 완료 후 재부팅
sudo reboot

```

재부팅 후 로그인하여 `nvidia-smi`를 확인합니다.
