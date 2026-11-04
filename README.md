# DevOps 워크스테이션 구축 실습

## 📌 프로젝트 개요
Windows 11 환경에서 Docker와 WSL2를 활용하여 DevOps 개발 환경을 구축하는 실습 프로젝트입니다.

## 🛠️ 설치된 도구
- **VS Code** (v1.137.0): 코드 편집기
- **Git** (v2.56.0): 버전 관리
- **Docker Desktop** (v29.9.2): 컨테이너 관리
- **WSL2**: Linux 환경

## 📂 프로젝트 구조

devops-practice/
├── docker/
├── app/
│   └── index.html
├── Dockerfile
├── docs/
├── screenshots/
├── scripts/
├── web-content/
└── README.md

## 🎓 학습 내용

### 1단계: Docker 설치 및 버전 확인
**명령어:**
\\\ash
docker --version
docker info
\\\
**결과:** ✅ Docker Desktop v29.9.2 설치 완료

### 2단계: 기본 명령어 학습
**명령어:**
\\\ash
docker ps -a
docker images
docker ps
\\\
**결과:** ✅ 컨테이너/이미지 목록 확인

### 3단계: Dockerfile 작성
**내용:** Nginx 기반 웹 서버 구성
\\\dockerfile
FROM nginx:latest
COPY app/index.html /usr/share/nginx/html/
EXPOSE 80
\\\
**결과:** ✅ Dockerfile 생성 완료

### 4단계: 이미지 빌드
**명령어:**
\\\ash
docker build -t devops-web .
docker images
\\\
**결과:** ✅ 이미지 빌드 성공

### 5단계: 컨테이너 실행
**명령어:**
\\\ash
docker run -p 8080:80 --name my-nginx devops-web
\\\
**결과:** ✅ 포트 8080에서 실행 중

### 6단계: 브라우저 접속 확인
**접속 주소:** http://localhost:8080
**결과:** ✅ 웹 페이지 정상 표시

### 7단계: 로그 확인
**명령어:**
\\\ash
docker logs my-nginx
docker logs -f my-nginx
\\\
**결과:** ✅ 컨테이너 로그 확인 및 모니터링

### 8단계: 파일 권한 설정
**명령어:**
\\\ash
ls -la docker/app/index.html
chmod 644 docker/app/index.html
\\\
**결과:** ✅ 파일 접근 권한 확보

### 9단계: 바인드 마운트
**명령어:**
\\\ash
docker run -p 8080:80 -v C:\Users\somed\Documents\devops-practice\docker\app:/usr/share/nginx/html --name my-nginx devops-web
\\\
**결과:** ✅ 브라우저에서 http://localhost:8080 접속 성공

### 10단계: Docker 볼륨
**명령어:**
\\\ash
docker volume create my-volume
docker run -p 8080:80 -v my-volume:/usr/share/nginx/html --name my-nginx-volume devops-web
\\\
**결과:** ✅ 컨테이너 제거 후에도 데이터 유지

### 11단계: 볼륨 영속성 검증
**명령어:**
\\\ash
docker cp docker/app/index.html my-nginx-volume:/usr/share/nginx/html/
\\\
**결과:** ✅ 파일 복사 성공, 데이터 영속성 확인

### 12단계: 이미지 정리
**명령어:**
\\\ash
docker rmi <image-id>
\\\
**결과:** ✅ 불필요한 이미지 제거

### 13단계: Git 커밋 및 GitHub 업로드
**명령어:**
\\\ash
git add .
git commit -m "Add DevOps practice project"
git push origin main
\\\
**결과:** ✅ GitHub에 성공적으로 업로드

## 🔧 트러블슈팅

### 트러블슈팅 1: 포트 충돌 문제
**문제:**
\\\
Error response from daemon: Bind for 0.0.0.0:8080 failed: port is already allocated
\\\
**원인:** 포트 8080이 이미 사용 중

**해결 방법:**
\\\ash
docker ps -a
docker rm <container-name>
docker run -p 8081:80 devops-web
\\\
**결과:** ✅ 포트 8081로 성공적으로 실행

### 트러블슈팅 2: 한글 인코딩 문제
**문제:** README.md 파일에서 한글이 깨져서 표시됨

**해결 방법:**
1. VS Code에서 README.md 열기
2. 우측 하단 상태바에서 인코딩 클릭
3. "UTF-8" 선택
4. 저장 (Ctrl+S)

**결과:** ✅ 한글이 정상적으로 표시됨

### 트러블슈팅 3: 파일 권한 문제
**문제:** Docker 컨테이너에서 호스트 파일에 접근 불가

**해결 방법:**
\\\ash
chmod 644 docker/app/index.html
\\\
**결과:** ✅ 파일 접근 권한 확보

## 📸 스크린샷 증거 (20개)

| # | 파일명 | 설명 |
|---|--------|------|
| 1 | 01-docker-version.png | Docker 버전 확인 |
| 2 | 02-docker-info.png | Docker 정보 확인 |
| 3 | 03-docker-ps-all.png | 모든 컨테이너 목록 |
| 4 | 04-docker-images.png | Docker 이미지 목록 |
| 5 | 05-dir-before.png | 디렉토리 생성 전 |
| 6 | 06-icacls-command.png | 권한 변경 명령 |
| 7 | 07-dir-after.png | 디렉토리 생성 후 |
| 8 | 08-docker-build-success.png | 빌드 성공 |
| 9 | 09-docker-ps-running.png | 실행 중인 컨테이너 |
| 10 | 10-git-commit-terminal.png | Git 커밋 터미널 |
| 11 | 11-github-commits-page.png | GitHub 커밋 페이지 |
| 12 | 12-browser-localhost-8080.png | 브라우저 접속 |
| 13 | 13-docker-logs-devops-web.png | 로그 확인 |
| 14 | 14-dockerfile-content.png | Dockerfile 내용 |
| 15 | 15-바인드마운트-브라우저확인.png | 바인드 마운트 확인 |
| 16 | 16-docker-build-command.png | 빌드 명령 |
| 17 | 17-Docker볼륨-브라우저확인.png | Docker 볼륨 확인 |
| 18 | 18-docker-images-result.png | 이미지 결과 |
| 19 | 19-볼륨영속성성공.png | 볼륨 영속성 성공 |
| 20 | 20-git-log-oneline.png | Git 로그 확인 |

## 🎓 핵심 개념 정리

### Docker 명령어
- \docker run\: 컨테이너 실행
- \docker ps\: 실행 중인 컨테이너 목록
- \docker logs\: 컨테이너 로그 확인
- \docker volume\: 볼륨 관리
- \docker rmi\: 이미지 제거

### 포트 매핑
- \-p 8080:80\: 호스트 포트 8080을 컨테이너 포트 80으로 매핑
- 브라우저에서 http://localhost:8080 접속

### 바인드 마운트
- \-v /host/path:/container/path\: 호스트 디렉토리를 컨테이너에 마운트
- 호스트 파일 변경이 실시간으로 컨테이너에 반영

### Docker 볼륨
- 컨테이너 제거 후에도 데이터 유지
- 여러 컨테이너 간 데이터 공유 가능

## 🔗 GitHub 저장소
https://github.com/nanhwasong/devops-practice

## ✅ 완료 체크리스트
- [x] Docker 설치 및 설정
- [x] 기본 명령어 학습
- [x] Dockerfile 작성
- [x] 이미지 빌드
- [x] 컨테이너 실행
- [x] 포트 매핑
- [x] 바인드 마운트
- [x] Docker 볼륨
- [x] 볼륨 영속성 검증
- [x] Git 커밋
- [x] GitHub 업로드
- [x] README.md 작성

## 🎯 결론
이 실습을 통해 Docker의 기본 개념, 포트 매핑, 바인드 마운트, 볼륨 관리, 로그 모니터링 등 DevOps의 핵심 기술을 습득했습니다.