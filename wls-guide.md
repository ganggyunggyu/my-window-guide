# 🐧 Windows에서 WSL(Windows Subsystem for Linux) 설치 및 설정 가이드  
> 완전 생짜 Windows PC에서 macOS처럼 리눅스 명령어 환경 세팅하기

---

## 1️⃣ WSL이 뭐냐
WSL(Windows Subsystem for Linux)은 Windows 위에서 **리눅스 커맨드를 네이티브처럼 실행할 수 있게 해주는 기능**이다.  
터미널에서 `ls`, `grep`, `vim`, `git`, `python` 같은 명령을 바로 쓸 수 있다.  
윈도우용 Docker, Node.js, 개발툴도 전부 연동 가능.

---

## 2️⃣ 사전 요구사항
- **운영체제**: Windows 10 2004 (빌드 19041) 이상 or Windows 11  
- **관리자 권한** 필요  
- **인터넷 연결** 필수 (Ubuntu 등 배포판 다운로드용)

---

## 3️⃣ 설치 단계 (CLI 기준 — 가장 빠름)
Windows PowerShell을 **관리자 권한**으로 열고 아래를 그대로 입력:

```powershell
# 1. WSL 및 VirtualMachinePlatform 기능 활성화
wsl --install

# 2. (선택) 기본 배포판을 Ubuntu로 지정
wsl --install -d Ubuntu

# 3. 재부팅
shutdown /r /t 0

이후 재부팅되면 WSL이 자동으로 Ubuntu 다운로드 시작 →
잠시 기다리면 새 UNIX 사용자 이름 입력: 프롬프트가 뜬다.
원하는 이름을 넣으면 기본 설정 완료.

⸻

4️⃣ GUI로 설치하는 방법 (초보자용)
	1.	Microsoft Store 열기
	2.	검색창에 “Ubuntu” 입력
	3.	“Ubuntu 22.04 LTS” 선택 후 “설치” 클릭
	4.	설치 완료 후 “시작” 버튼 누르면 터미널 창이 뜬다.
(첫 실행 시 리눅스 유저 계정 생성 진행됨)

⸻

5️⃣ 설치 확인 및 버전 점검

wsl --list --verbose     # 설치된 배포판 확인
wsl --status             # 현재 상태 확인
wsl --set-default Ubuntu # 기본 배포판 설정
wsl --set-version Ubuntu 2  # WSL2로 변경


⸻

6️⃣ 리눅스 명령어 바로 쓰기

이제 PowerShell, CMD, VSCode 터미널 어디서든 다음이 가능하다:

wsl                     # 리눅스 쉘 진입
ls, cat, grep, vim ...  # 바로 사용 가능
exit                    # 윈도우로 복귀

또는 Windows 경로에서도 직접:

wsl ls -al
wsl cat /etc/os-release


⸻

7️⃣ macOS처럼 세팅하기

① 패키지 관리자 업데이트

sudo apt update && sudo apt upgrade -y

② 자주 쓰는 툴 설치

sudo apt install -y build-essential curl wget git zsh vim htop tree

③ Zsh + Oh My Zsh (mac 스타일 쉘)

sudo apt install zsh -y
chsh -s $(which zsh)
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

④ 추천 테마

# powerlevel10k 설치 (zsh 테마)
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/.oh-my-zsh/custom/themes/powerlevel10k

.zshrc 파일 열고:

ZSH_THEME="powerlevel10k/powerlevel10k"


⸻

8️⃣ 파일 경로 주의
	•	리눅스 → 윈도우 경로 접근:

cd /mnt/c/Users/YourName/Desktop


	•	윈도우에서 리눅스 홈 접근:

\\wsl$\Ubuntu\home\yourname



⸻

9️⃣ VSCode 연동

# 리눅스 터미널에서
code .

자동으로 VSCode Remote - WSL 확장이 설치되고,
윈도우 VSCode가 리눅스 내부 환경과 직접 연결된다.

⸻

🔟 문제 해결

증상	원인	해결
wsl 명령어 인식 안 됨	Windows 버전 낮음	Windows Update 후 재시도
네트워크 안됨	VPN, 방화벽 간섭	관리자 PowerShell에서 wsl --shutdown 후 재시작
Docker 안됨	Hyper-V 비활성화	bcdedit /set hypervisorlaunchtype auto 후 재부팅


⸻

✅ 마무리

이제 WSL 환경에서 ls, vim, grep, npm, python 등
모든 리눅스 명령을 맥처럼 쓸 수 있다.
원하면 alias로 macOS 터미널과 거의 동일한 환경 구성 가능.

⸻

추천 조합
	•	Ubuntu 22.04 LTS
	•	zsh + Oh My Zsh + Powerlevel10k
	•	VSCode Remote WSL

⸻

작성자: 감겸규
버전: 2025.11
목적: Windows 개발 환경을 맥처럼 세팅하기 위한 완전 기초 가이드

