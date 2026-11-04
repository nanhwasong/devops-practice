DevOps 개발 워크스테이션 구축
Windows 11 환경에서 VS Code, Git, GitHub, WSL2, Docker Desktop을 이용해 개발용 워크스테이션을 구축하고, 터미널 기본 조작부터 Docker 이미지/컨테이너, 포트 매핑, 바인드 마운트, 볼륨 영속성, Git/GitHub 연동까지 직접 실행하고 검증한 프로젝트입니다.
단순히 설치 여부만 확인하지 않고, 실제 명령과 출력 결과를 `evidence/terminal/`에 기록하고 주요 화면은 `screenshots/`에 정리했습니다.
---
1. 실행 환경
OS: Windows 11
Shell: Windows PowerShell
Linux 환경: WSL2 / Ubuntu
Editor: Visual Studio Code
Docker: Docker Desktop 29.9.2
Git: Git CLI
GitHub 연결: SSH 방식
기본 브랜치: `main`
Docker 환경 확인
```powershell
docker --version
docker info
```
확인 결과 Docker Client와 Server가 모두 정상 동작했고, Docker Desktop의 Linux 컨테이너 환경이 WSL2 기반으로 실행되는 것을 확인했습니다.
실행 로그: `evidence/terminal/03_docker_environment.txt`
Docker 버전 화면: `screenshots/03_docker/01_docker_version.png`
Docker 정보 화면: `screenshots/03_docker/02_docker_info.png`
---
2. 프로젝트 구조
```text
devops-practice/
├── docker/
│   ├── Dockerfile
│   └── app/
│       └── index.html
├── web-content/
│   └── index.html
├── evidence/
│   └── terminal/
│       ├── 01_basic_commands.txt
│       ├── 02_permissions.txt
│       ├── 02_permissions_linux.txt
│       ├── 03_docker_environment.txt
│       ├── 04_hello_world.txt
│       ├── 05_docker_operations.txt
│       ├── 06_ubuntu_container.txt
│       ├── 07_custom_image.txt
│       ├── 08_bind_mount.txt
│       ├── 09_volume_persistence.txt
│       ├── 10_git_github.txt
│       ├── 10_git_github_verified.txt
│       ├── 11_port_troubleshooting.txt
│       └── 12_cleanup.txt
├── screenshots/
│   ├── 01_environment/
│   ├── 02_git_ssh/
│   ├── 03_docker/
│   ├── 04_storage/
│   └── 05_troubleshooting/
└── README.md
```
구조는 역할별로 나눴습니다.
`docker/`: Docker 이미지 빌드에 필요한 파일
`docker/app/`: Nginx에서 서비스할 웹 콘텐츠
`web-content/`: 바인드 마운트 실습용 파일
`evidence/terminal/`: 실제 터미널 명령과 출력 로그
`screenshots/`: 실행 과정과 결과를 시각적으로 확인할 수 있는 증거
`README.md`: 전체 수행 과정과 원리를 한 문서에서 확인하도록 정리
프로젝트 내부에서는 가능한 한 상대 경로를 사용하고, Windows 호스트의 실제 경로를 Docker에 직접 연결해야 하는 바인드 마운트에서는 절대 경로를 사용했습니다.
---
3. 터미널 기본 조작
PowerShell에서 현재 위치 확인, 숨김 파일 포함 목록 확인, 폴더 이동, 파일 생성, 내용 확인, 복사, 이동/이름 변경, 삭제를 직접 수행했습니다.
```powershell
Get-Location
Get-ChildItem -Force
Set-Location
New-Item
Get-Content
Copy-Item
Move-Item
Remove-Item
```
실습에서는 `practice-temp` 폴더를 별도로 만들어 기존 프로젝트 파일에 영향을 주지 않도록 했습니다.
실제 실행 로그: `evidence/terminal/01_basic_commands.txt`
디렉터리 변경 전 화면: `screenshots/01_environment/01_dir_before.png`
디렉터리 변경 후 화면: `screenshots/01_environment/03_dir_after.png`
---
4. 파일 및 디렉터리 권한
Linux 파일 권한은 `r`, `w`, `x`로 표현합니다.
`r` = 읽기 = 4
`w` = 쓰기 = 2
`x` = 실행 = 1
각 숫자는 순서대로 `소유자 / 그룹 / 기타 사용자` 권한을 뜻합니다.
```text
644 = rw- r-- r--
755 = rwx r-x r-x
```
WSL 내부 Linux 파일시스템에서 파일과 디렉터리 권한을 직접 변경했습니다.
파일 권한
```bash
chmod 600 /root/devops-permission-test/test.txt
chmod 644 /root/devops-permission-test/test.txt
```
변경 전 `-rw-------`, 변경 후 `-rw-r--r--`를 확인했습니다.
디렉터리 권한
```bash
chmod 700 /root/devops-permission-test
chmod 755 /root/devops-permission-test
```
변경 전 `drwx------`, 변경 후 `drwxr-xr-x`를 확인했습니다.
Linux 권한 변경 로그: `evidence/terminal/02_permissions_linux.txt`
Windows 권한 확인 화면: `screenshots/01_environment/02_icacls_command.png`
Windows 경로와 WSL 권한 차이
처음에는 `/mnt/c/...`에 있는 Windows 파일에서 `chmod 644`를 실행했지만 `-rwxrwxrwx` 표시가 그대로 유지됐습니다. Windows 드라이브가 WSL에 마운트되는 방식 때문에 Linux 권한 비트가 일반 Linux 파일시스템과 동일하게 동작하지 않을 수 있음을 확인했습니다.
따라서 실제 권한 변경 실습은 WSL 내부 Linux 파일시스템에서 다시 수행했습니다.
비교 로그: `evidence/terminal/02_permissions.txt`
---
5. Docker 설치와 기본 실행
Docker 설치 후 가장 기본적인 테스트로 `hello-world` 이미지를 실행했습니다.
```powershell
docker run --rm hello-world
```
출력에서 다음 문구를 확인했습니다.
```text
Hello from Docker!
```
이 실행을 통해 Docker Client가 Docker daemon과 통신하고, 이미지를 가져와 컨테이너를 생성하고 실행하는 전체 흐름이 정상임을 확인했습니다.
실행 로그: `evidence/terminal/04_hello_world.txt`
---
6. Ubuntu 컨테이너 실습
Ubuntu 이미지를 내려받고 컨테이너를 백그라운드로 실행했습니다.
```powershell
docker run -d --name ubuntu-practice ubuntu sleep infinity
```
실행 중인 컨테이너 내부에서 다음 명령을 실행했습니다.
```powershell
docker exec ubuntu-practice pwd
docker exec ubuntu-practice ls
docker exec ubuntu-practice echo "Hello Docker from Ubuntu"
```
이후 컨테이너를 중지하고 `docker ps -a`에서 `Exited` 상태를 확인한 뒤 다시 시작했습니다.
```powershell
docker stop ubuntu-practice
docker start ubuntu-practice
```
이를 통해 `docker stop`은 컨테이너를 삭제하지 않고 중지만 하며, `docker start`로 다시 실행할 수 있다는 점을 확인했습니다.
전체 실행 로그: `evidence/terminal/06_ubuntu_container.txt`
---
7. Docker 기본 운영 명령
```powershell
docker images
docker ps
docker ps -a
docker logs devops-app
docker stats --no-stream devops-app
```
확인 결과 이미지 목록, 실행 중 컨테이너, 전체 컨테이너, Nginx 로그, 실제 HTTP `200` 요청 기록, CPU/Memory/Network 사용량을 확인했습니다.
현재 운영 중인 `devops-app`은 호스트 `8080` 포트를 컨테이너 `80` 포트에 연결해 실행했습니다.
운영 로그: `evidence/terminal/05_docker_operations.txt`
컨테이너 목록: `screenshots/03_docker/04_docker_ps_all.png`
이미지 목록: `screenshots/03_docker/05_docker_images.png`
Docker 로그 화면: `screenshots/03_docker/11_docker_logs_devops_web.png`
실시간 로그 모니터링: `screenshots/03_docker/20_realtime_logs_monitoring.png`
---
8. Docker 이미지와 컨테이너의 차이
```text
Dockerfile
    ↓ build
Docker Image
    ↓ run
Container
```
이미지는 Dockerfile을 기반으로 만든 실행 템플릿이고, 컨테이너는 이미지를 실제로 실행한 인스턴스입니다.
컨테이너 내부에서 파일을 변경할 수 있지만, 컨테이너를 삭제하면 그 내부 변경 내용도 함께 사라질 수 있습니다. 따라서 영구적으로 보존해야 하는 데이터에는 Docker 볼륨이나 바인드 마운트를 사용합니다.
---
9. Nginx 기반 커스텀 이미지 제작
이번 프로젝트에서는 `nginx:alpine`을 베이스 이미지로 사용했습니다.
```dockerfile
FROM nginx:alpine

WORKDIR /usr/share/nginx/html

COPY app/index.html .

EXPOSE 80
```
직접 작성한 `index.html`을 포함해 커스텀 웹 이미지를 만들었습니다.
이미지 빌드
```powershell
docker build -t devops-web-verified .\docker
```
BuildKit 로그에서 다음 내용을 확인했습니다.
```text
naming to docker.io/library/devops-web-verified:latest done
```
컨테이너 실행
기존 `8080`은 이미 사용 중이어서 검증용 컨테이너는 `8081`을 사용했습니다.
```powershell
docker run -d -p 8081:80 --name devops-web-verified-container devops-web-verified
```
웹 접속
```powershell
curl.exe http://localhost:8081
```
HTML 응답을 정상 수신했고 브라우저에서도 한글 페이지가 정상 표시되는 것을 확인했습니다.
커스텀 이미지 전체 로그: `evidence/terminal/07_custom_image.txt`
Dockerfile 화면: `screenshots/03_docker/12_dockerfile_content.png`
빌드 명령 화면: `screenshots/03_docker/13_docker_build_command.png`
빌드 성공 화면: `screenshots/03_docker/03_docker_build_success_1.png`
브라우저 접속 화면: `screenshots/03_docker/10_browser_localhost_8080_1.png`
---
10. 포트 매핑 원리
컨테이너는 호스트와 분리된 네트워크 환경을 사용합니다. 컨테이너 내부의 80번 포트가 Windows 호스트의 80번 포트와 자동으로 연결되는 것은 아닙니다.
```powershell
docker run -d -p 8081:80 ...
```
```text
Windows host :8081
        ↓
Docker container :80
```
브라우저에서 `localhost:8081`로 접속하면 Docker가 요청을 컨테이너의 80번 포트로 전달합니다.
---
11. 절대 경로와 상대 경로
상대 경로는 프로젝트 내부 파일을 가리킬 때 사용했습니다.
```text
.\docker
.\docker\app
```
프로젝트가 다른 위치로 이동해도 같은 구조를 사용할 수 있어 재현성이 좋습니다.
반면 바인드 마운트처럼 Windows 호스트의 실제 위치를 Docker에 직접 알려줘야 하는 경우에는 절대 경로를 사용했습니다.
```text
C:\Users\somed\Documents\devops-practice\docker\app
```
WSL에서는 Windows C 드라이브가 다음처럼 보입니다.
```text
/mnt/c/Users/somed/Documents/devops-practice
```
---
12. 바인드 마운트
호스트의 `docker/app` 폴더를 Nginx 컨테이너의 웹 루트에 직접 연결했습니다.
```powershell
docker run -d -p 8082:80 `
-v C:\Users\somed\Documents\devops-practice\docker\app:/usr/share/nginx/html `
--name bind-mount-test nginx:alpine
```
브라우저에서 `http://localhost:8082`에 접속한 뒤 호스트의 `index.html`을 수정했습니다.
```html
<h1>바인드 마운트 변경 확인!</h1>
```
Docker 이미지를 다시 빌드하지 않았는데도 브라우저를 새로고침하자 변경 내용이 즉시 반영됐습니다.
실행 로그: `evidence/terminal/08_bind_mount.txt`
변경 결과 브라우저 화면: `screenshots/04_storage/08_bind_mount_changed.png`
기존 바인드 마운트 증빙: `screenshots/04_storage/01_bind_mount_browser_check.png`
실행 명령 화면: `screenshots/04_storage/05_bind_mount_docker_run.jpg`
---
13. Docker 볼륨과 데이터 영속성
테스트용 볼륨을 만들었습니다.
```powershell
docker volume create devops-volume-test
```
첫 번째 컨테이너에서 볼륨 안에 데이터를 만들었습니다.
```powershell
docker run -d --name volume-test-1 -v devops-volume-test:/data ubuntu sleep infinity
docker exec volume-test-1 bash -c "echo persistent-data > /data/test.txt"
docker exec volume-test-1 cat /data/test.txt
```
결과:
```text
persistent-data
```
첫 번째 컨테이너를 삭제했습니다.
```powershell
docker rm -f volume-test-1
```
그 후 두 번째 컨테이너를 같은 볼륨에 연결했습니다.
```powershell
docker run -d --name volume-test-2 -v devops-volume-test:/data ubuntu sleep infinity
docker exec volume-test-2 cat /data/test.txt
```
다시 `persistent-data`가 출력되어 컨테이너를 삭제해도 데이터는 Docker 볼륨에 유지되는 것을 확인했습니다.
영속성 검증 로그: `evidence/terminal/09_volume_persistence.txt`
볼륨 확인 화면: `screenshots/04_storage/02_docker_volume_browser_check.png`
영속성 성공 화면: `screenshots/04_storage/03_volume_persistence_success.png`
바인드 마운트와 Docker 볼륨
바인드 마운트: 호스트의 실제 폴더를 직접 연결하며 소스코드 실시간 반영에 적합
Docker 볼륨: Docker가 별도로 관리하며 컨테이너와 데이터 생명주기를 분리하는 데 적합
중요한 데이터는 볼륨 사용과 함께 별도 백업도 고려해야 합니다.
---
14. Git과 GitHub 연동
```powershell
git config --get user.name
git config --get user.email
git config --global init.defaultBranch
git remote -v
git branch --show-current
```
최종 확인 결과:
```text
user.name = nanhwasong
default branch = main
remote = git@github.com:nanhwasong/devops-practice.git
current branch = main
```
Git은 로컬 파일의 변경 이력과 버전을 관리하고, GitHub는 Git 저장소를 원격에 보관하고 공유·협업하는 플랫폼입니다.
```text
VS Code
   ↓
Local Git
   ↓ push / pull
GitHub Repository
```
최종 검증 로그: `evidence/terminal/10_git_github_verified.txt`
GitHub SSH 연결 성공 화면: `screenshots/02_git_ssh/13_github_ssh_connection_success.png`
SSH 연결 테스트 화면: `screenshots/02_git_ssh/14_ssh_connection_test.png`
Git 커밋 터미널: `screenshots/02_git_ssh/09_git_commit_terminal.png`
GitHub 커밋 화면: `screenshots/02_git_ssh/10_github_commits_page.png`
> 공개 저장소에는 비밀번호, Personal Access Token, SSH 개인키, 인증 코드 등 민감정보를 포함하지 않도록 주의합니다.
---
15. 트러블슈팅
15-1. 호스트 포트 충돌
이미 `devops-app`이 호스트 8080 포트를 사용 중인 상태에서 다른 Nginx 컨테이너를 같은 8080으로 실행했습니다.
```powershell
docker run -d -p 8080:80 --name port-conflict-test nginx:alpine
```
오류:
```text
Bind for 0.0.0.0:8080 failed: port is already allocated
```
원인이 기존 컨테이너 또는 다른 프로세스의 포트 점유라고 보고 다음 순서로 확인했습니다.
```powershell
docker ps -a
netstat -ano | findstr :8080
```
`devops-app`에서 `0.0.0.0:8080->80/tcp` 매핑을 확인했고 Windows에서도 8080 포트가 `LISTENING` 상태임을 확인했습니다.
실패한 테스트 컨테이너를 정리한 뒤 호스트 포트를 8083으로 변경했습니다.
```powershell
docker run -d -p 8083:80 --name port-solution-test nginx:alpine
curl.exe -s -o NUL -w "%{http_code}" http://localhost:8083
```
결과:
```text
200
```
전체 문제 해결 로그: `evidence/terminal/11_port_troubleshooting.txt`
진단 흐름은 다음과 같이 정리할 수 있습니다.
```text
실행 실패
→ docker ps -a 확인
→ netstat으로 호스트 포트 확인
→ 사용 중인 포트/프로세스 확인
→ 기존 서비스 종료 여부 판단
→ 필요하면 다른 호스트 포트 사용
→ HTTP 응답으로 재검증
```
15-2. PowerShell에서 curl 한글 출력 깨짐
브라우저에서는 한글이 정상적으로 표시되었지만 PowerShell의 `curl.exe`로 HTML을 출력했을 때 한글이 깨져 보이는 현상이 있었습니다.
HTML 파일을 UTF-8로 확인하고 브라우저에서 다시 접속한 결과 한글이 정상적으로 표시됐습니다. 따라서 웹 서버 자체의 문제가 아니라 PowerShell 콘솔에서 curl 응답을 출력하는 과정의 문자 인코딩 차이로 판단했습니다.
인코딩 수정 전: `screenshots/05_troubleshooting/01_encoding_before.png`
인코딩 수정 후: `screenshots/05_troubleshooting/02_encoding_after.png`
15-3. 컨테이너 이름 불일치
기존 문서에서 사용하던 `my-nginx`를 대상으로 로그와 리소스를 조회했지만 실제 실행 중인 컨테이너 이름은 `devops-app`이었습니다.
```powershell
docker logs my-nginx
```
결과:
```text
No such container: my-nginx
```
`docker ps`로 실제 컨테이너 이름을 확인한 뒤 다음처럼 다시 실행했습니다.
```powershell
docker logs devops-app
docker stats --no-stream devops-app
```
이후 로그와 리소스 정보가 정상 출력됐습니다.
---
16. 테스트 컨테이너 정리
실습에서 만든 테스트 컨테이너는 모두 정리했습니다.
```powershell
docker rm -f ubuntu-practice
docker rm -f volume-test-2
docker rm -f bind-mount-test
docker rm -f devops-web-verified-container
docker rm -f port-solution-test
```
정리 후 기존 `devops-app`만 남는 것을 확인했습니다.
정리 전/후 로그: `evidence/terminal/12_cleanup.txt`
이미지 정리 화면: `screenshots/03_docker/17_image_cleanup_complete.png`
---
17. 핵심 개념 정리
Git과 GitHub
Git: 내 컴퓨터에서 파일 변경 이력을 관리하는 버전관리 시스템
GitHub: Git 저장소를 원격에 보관하고 공유·협업하는 플랫폼
이미지와 컨테이너
이미지: 컨테이너를 만들기 위한 템플릿
컨테이너: 이미지를 실제로 실행한 인스턴스
포트 매핑
컨테이너는 독립된 네트워크 환경을 사용하기 때문에 호스트에서 접속하려면 호스트 포트와 컨테이너 포트를 연결해야 합니다.
바인드 마운트
호스트 파일을 컨테이너에 직접 연결하여 호스트 수정 내용을 즉시 반영할 수 있습니다.
Docker 볼륨
컨테이너와 데이터 저장 공간을 분리하여 컨테이너가 삭제돼도 데이터를 유지할 수 있습니다.
파일 권한
```text
r = 4
w = 2
x = 1
```
`644`는 `rw-r--r--`, `755`는 `rwxr-xr-x`로 해석합니다.
절대 경로와 상대 경로
상대 경로: 프로젝트 내부에서 재현성을 높일 때 사용
절대 경로: 호스트의 실제 파일 위치가 필요한 바인드 마운트 등에 사용
---
18. 수행 결과 요약
수행 내용	검증 방법	증거
터미널 기본 명령	생성/복사/이동/삭제	`01_basic_commands.txt`
파일/디렉터리 권한	600→644, 700→755	`02_permissions_linux.txt`
Docker 설치	`docker --version`, `docker info`	`03_docker_environment.txt`
hello-world	`docker run --rm hello-world`	`04_hello_world.txt`
Docker 운영	images / ps / logs / stats	`05_docker_operations.txt`
Ubuntu 컨테이너	exec / stop / start	`06_ubuntu_container.txt`
커스텀 이미지	Dockerfile build/run	`07_custom_image.txt`
포트 접속	curl / 브라우저	`07_custom_image.txt`
바인드 마운트	호스트 파일 실시간 반영	`08_bind_mount.txt`
볼륨 영속성	컨테이너 삭제 후 데이터 재확인	`09_volume_persistence.txt`
Git/GitHub	config / remote / branch / SSH	`10_git_github_verified.txt` + 스크린샷
포트 충돌	문제 재현 → 확인 → 8083 해결	`11_port_troubleshooting.txt`
테스트 정리	컨테이너 삭제 전/후	`12_cleanup.txt`
---
19. 마무리
이번 실습에서는 개발 도구를 설치하는 것에서 끝내지 않고, 실제 터미널 명령과 Docker 컨테이너를 이용해 개발 워크스테이션의 기본 구조를 직접 구성하고 검증했습니다.
특히 터미널 기반 파일/디렉터리 작업, Linux 파일 권한, Docker 이미지와 컨테이너, Ubuntu 컨테이너 내부 명령, Dockerfile 기반 커스텀 이미지, 포트 매핑, 바인드 마운트, Docker 볼륨 영속성, Git과 GitHub 연동, 포트 충돌과 문자 인코딩 문제 해결을 실제 실행을 통해 확인했습니다.
모든 주요 실행 결과는 `evidence/terminal/`과 `screenshots/`에 함께 기록하여 저장소만으로 전체 수행 과정과 검증 결과를 확인할 수 있도록 구성했습니다.