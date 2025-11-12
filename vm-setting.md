좋아요 — 이 오류 메시지는 **WSL2(Windows Subsystem for Linux 2)** 를 설치하려 할 때, Windows에서 **가상화 기능이 비활성화되어 있거나 “가상 머신 플랫폼(Virtual Machine Platform)” 옵션이 켜져 있지 않아서** 발생하는 전형적인 문제입니다.
오류 코드 `HCS_E_HYPERV_NOT_INSTALLED` 는 “Hyper-V(또는 필요한 하위 구성요소)가 설치되어 있지 않음”을 의미합니다.

---

## ✅ 해결 방법 단계별 안내

### 1️⃣ BIOS에서 가상화(Virtualization) 활성화하기

1. 컴퓨터를 **재부팅**합니다.
2. 부팅 시 **BIOS/UEFI 설정**으로 들어갑니다. (제조사에 따라 `F2`, `F10`, `F12`, `DEL` 키 등 사용)
3. 메뉴에서 다음 항목을 찾습니다:

   * **Intel VT-x**, **Intel Virtualization Technology**
   * **AMD-V**, **SVM Mode**
4. 해당 항목을 **Enabled(활성화)** 로 변경합니다.
5. 저장(Save) 후 **재부팅**합니다.

---

### 2️⃣ Windows 기능에서 “가상 머신 플랫폼” 켜기

PowerShell(관리자 권한)에서 아래 명령을 실행하세요:

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

그리고 WSL 자체를 다시 활성화합니다:

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

---

### 3️⃣ 시스템 재부팅

위의 명령을 모두 실행한 후 **컴퓨터를 재부팅**합니다.

---

### 4️⃣ WSL 2를 기본 버전으로 설정

재부팅 후 PowerShell에서 아래 명령을 실행하세요:

```powershell
wsl --set-default-version 2
```

---

### 5️⃣ 다시 Ubuntu 설치

이제 다시 시도하세요:

```powershell
wsl --install -d Ubuntu
```

---

### 💡 확인 방법

가상화가 제대로 활성화되어 있는지 확인하려면 PowerShell에서 다음 명령을 실행하세요:

```powershell
systeminfo | find "가상화"
```

결과에 `가상화 사용 가능: 예` 라고 표시되어야 합니다.

---

원하신다면 제가 **“이 명령들을 한 번에 실행할 수 있는 스크립트(PowerShell용)”** 를 만들어드릴 수도 있습니다.
그걸 실행하면 BIOS 설정만 직접 켜주시면 되고, 나머지는 자동으로 됩니다.
원하시나요?
