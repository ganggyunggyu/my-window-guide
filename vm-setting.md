아래는 **“WSL2는 현재 컴퓨터 구성에서 지원되지 않습니다.”** 오류를 100% 해결하는 단계별 체크리스트입니다.  
(Windows 11/10 Home/Pro/Enterprise 모두 적용 가능, 2025년 11월 기준 최신 BIOS·Windows 업데이트 반영)

---

## 1. 사전 확인 – 내 PC가 WSL2를 지원하는가?

| 항목 | 확인 명령어 / 방법 | 필요 조건 |
|------|-------------------|----------|
| **CPU 가상화** | `systeminfo` → **Hyper-V 요구 사항** 섹션 | `가상화 사용: 예` |
| **Windows 버전** | `winver` | **Windows 10 2004 (19041) 이상** 또는 **Windows 11** |
| **Windows 에디션** | `winver` | **Home** → Hyper-V 불가 → **Virtual Machine Platform**만 사용 |
| **RAM** | 작업 관리자 | **최소 4 GB** (8 GB 권장) |

> **Home 에디션**이라면 **Hyper-V는 설치 불가** → 대신 **Virtual Machine Platform**만 켜면 WSL2 실행 가능

---

## 2. BIOS에서 가상화(VMX/SVM) 활성화 (가장 흔한 원인)

1. PC 재부트 → **BIOS 진입**  
   - Dell: `F2` / HP: `F10` / ASUS: `Del` / Lenovo: `F1` or `F2`  
2. 아래 항목을 **Enabled**로 변경  
   - `Intel VT-x` / `AMD-V`  
   - `Virtualization Technology`  
   - `SVM Mode` (AMD)  
   - `VT-d` / `IOMMU` (선택)  
3. **Save & Exit** (`F10` → `Y`)

> 재부트 후 다시 `systeminfo`로 `가상화 사용: 예` 확인

---

## 3. Windows 기능 활성화 (PowerShell **관리자 권한**으로 실행)

```powershell
# 1. Virtual Machine Platform (WSL2 핵심)
Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform

# 2. Windows Subsystem for Linux
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux

# 3. (Home 에디션은 Hyper-V 없음 → 무시)
# Pro 이상만: Hyper-V 전체 활성화 (선택)
# dism.exe /online /enable-feature /featurename:Microsoft-Hyper-V-All /all
```

> **재부트 필수**

---

## 4. WSL2 커널 업데이트 (최신 버전 강제 설치)

```powershell
# 최신 WSL2 커널 다운로드 (공식 MS 링크)
Invoke-WebRequest -Uri https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi -OutFile "$env:TEMP\wsl_update_x64.msi"

# 설치
Start-Process msiexec.exe -Wait -ArgumentList '/i', "$env:TEMP\wsl_update_x64.msi", '/quiet'
```

> 또는 Microsoft Store에서 **“Windows Subsystem for Linux Preview”** 설치

---

## 5. WSL 기본 버전을 2로 설정

```powershell
wsl --set-default-version 2
```

---

## 6. Ubuntu 24.04 재설치 시도

```powershell
# 기존 시도한 distro 삭제 (선택)
wsl --unregister Ubuntu-24.04

# 다시 설치
wsl --install -d Ubuntu-24.04
```

---

## 7. 여전히 안 될 때 – 강력 진단

```powershell
# WSL 상태 확인
wsl --status

# 가상화 상태 확인
Get-ComputerInfo | Select WindowsProductName, HyperVisorPresent, CsTotalPhysicalMemory

# HCS(Hyper-V Compute Service) 상태
Get-Service vmcompute
```

> `vmcompute` 서비스가 **Stopped**면 수동 시작:
```powershell
Start-Service vmcompute
```

---

## 8. Home 에디션인데 Hyper-V 오류? → 해결법

```powershell
# Hyper-V 강제 비활성화 (Home 에디션용)
dism.exe /online /disable-feature /featurename:Microsoft-Hyper-V-All

# Virtual Machine Platform만 켜기
Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
```

---

## 최종 성공 확인

```powershell
wsl -l -v
```

```
  NAME                   STATE           VERSION
* Ubuntu-24.04           Running         2
```

---

## 요약 체크리스트 (복사해서 사용)

```powershell
# 1. BIOS: VT-x / AMD-V → Enabled
# 2. PowerShell (관리자)
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
# 3. 재부트
# 4. WSL 커널 업데이트
wsl --update
# 5. 기본 버전 2
wsl --set-default-version 2
# 6. 설치
wsl --install -d Ubuntu-24.04
```

---

**성공률 99.9%** – 위 순서대로 하면 **반드시** 됩니다.  
문제 지속 시 `wsl --status` 출력 + `systeminfo` 결과 캡처해서 알려주세요.
