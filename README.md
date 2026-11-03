# DevOps 워크스테이션 구축 실습

## 📌 프로젝트 개요
Windows 11 환경에서 Docker와 WSL2를 활용하여 DevOps 개발 환경을 구축하는 실습 프로젝트입니다.

## 🛠️ 설치된 도구
| 도구 | 버전 | 용도 |
|------|------|------|
| VS Code | v1.137.0 | 코드 편집기 |
| Git | v2.56.0 | 버전 관리 |
| Docker Desktop | v29.9.2 | 컨테이너 관리 |
| WSL2 | - | Linux 환경 |

## 📁 프로젝트 구조
```
devops-practice/
├── docker/
│   ├── Dockerfile
│   └── app/
│       └── index.html
├── docs/
├── scripts/
├── screenshots/
└── README.md
```

## 🎓 학습 내용

### 1단계~10단계: 기본 환경 구성
- Docker 설치 및 설정
- 기본 명령어 학습
- 이미지 빌드

### 11단계: 바인드 마운트
**목표:** 호스트 파일을 컨테이너와 공유

**명령어:**
```ash
docker run -p 8080:80 -v C:\Users\somed\Documents\devops-practice\docker\app:/usr/share/nginx/html --name my-nginx nginx:latest
```

**결과:** ✅ 브라우저에서 http://localhost:8080 접속 성공

### 12단계: Docker 볼륨
**목표:** 데이터 영속성 확보

**명령어:**
```ash
docker volume create my-volume
docker run -p 8080:80 -v my-volume:/usr/share/nginx/html --name my-nginx-volume nginx:latest
```

**결과:** ✅ 컨테이너 제거 후에도 데이터 유지

### 13단계: 볼륨 영속성 검증
**명령어:**
```ash
docker cp docker/app/index.html my-nginx-volume:/usr/share/nginx/html/
```

**결과:** ✅ 파일 복사 성공, 데이터 영속성 확인

### 15단계: 이미지 정리
**명령어:**
```ash
docker rmi <image-id>
```

**결과:** ✅ 불필요한 이미지 제거

### 16단계: 로그 모니터링
**명령어:**
```ash
docker logs my-nginx
docker logs -f my-nginx  # 실시간 모니터링
```

**결과:** ✅ 컨테이너 로그 확인 및 모니터링

## 🔧 트러블슈팅

### 트러블슈팅 1: 포트 충돌 문제

**문제:**
```
Error response from daemon: driver failed programming external connectivity on endpoint my-nginx: 
Bind for 0.0.0.0:8080 failed: port is already allocated
```

**원인:**
- 포트 8080이 이미 다른 컨테이너/프로세스에서 사용 중

**해결 방법:**
1. 실행 중인 컨테이너 확인: \docker ps -a\
2. 기존 컨테이너 제거: \docker rm <container-name>\
3. 다른 포트로 실행: \docker run -p 8081:80 my-nginx\

**결과:** ✅ 포트 8081로 성공적으로 실행

---

### 트러블슈팅 2: 한글 인코딩 문제

**문제:**
- README.md 파일에서 한글이 깨져서 표시됨
- 예: "?뚰겕?ㅽ뀒?댁뀡" (원래: "워크스테이션")

**원인:**
- 파일이 UTF-8이 아닌 다른 인코딩(EUC-KR 등)으로 저장됨

**해결 방법:**
1. VS Code에서 README.md 열기
2. 우측 하단 상태바에서 현재 인코딩 클릭
3. "UTF-8" 선택
4. 저장 (Ctrl+S)

**결과:** ✅ 한글이 정상적으로 표시됨

---

### 트러블슈팅 3: 파일 권한 문제

**문제:**
- Docker 컨테이너에서 호스트 파일에 접근 불가

**원인:**
- 파일 권한 설정 미흡

**해결 방법:**
```ash
# 파일 권한 확인
ls -la docker/app/index.html

# 권한 변경 (필요시)
chmod 644 docker/app/index.html
```

**결과:** ✅ 파일 접근 권한 확보

## 📸 스크린샷 증거

| 단계 | 파일명 | 설명 |
|------|--------|------|
| 8 | 08-git-commit-terminal.png | Git 커밋 터미널 |
| 8 | 08-github-commits-page.png | GitHub 커밋 페이지 |
| 11 | 11_바인드마운트_브라우저확인.png | 바인드 마운트 브라우저 확인 |
| 12 | 12_Docker볼륨_브라우저확인.png | Docker 볼륨 브라우저 확인 |
| 13 | 13단계_볼륨영속성성공.png | 볼륨 영속성 성공 |
| 15 | 15단계_이미지정리완료.png | 이미지 정리 완료 |
| 16 | 16단계_로그확인.png | 로그 확인 |
| 16 | 16단계_실시간로그모니터링.png | 실시간 로그 모니터링 |
| 16 | 16단계_컨테이너상세정보.png | 컨테이너 상세 정보 |

## 🎓 학습 내용 정리

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

### 파일 권한
- Linux 권한 체계: rwx (읽기, 쓰기, 실행)
- 8진법 표기: 644 (rw-r--r--)

## 🔗 GitHub 저장소
https://github.com/nanhwasong/devops-practice

## 📝 커밋 이력
- 초기 프로젝트 구성
- Dockerfile 작성
- 바인드 마운트 구현
- 볼륨 영속성 검증
- 로그 모니터링 구현

## ✅ 완료 체크리스트
- [x] 1단계: 환경 설치
- [x] 2단계: GitHub 저장소 생성
- [x] 3단계: 폴더 구조 생성
- [x] 4단계: Dockerfile 작성
- [x] 5단계: 웹 서버 구성
- [x] 6단계: 이미지 빌드
- [x] 7단계: 포트 매핑
- [x] 8단계: UTF-8 인코딩
- [x] 9단계: 바인드 마운트
- [x] 10단계: 볼륨 생성/영속성
- [x] 11단계: Git 커밋
- [x] 12단계: 컨테이너/이미지 정리
- [x] 13단계: 로그 모니터링
- [x] 14단계: 트러블슈팅
- [x] 15단계: README.md 작성

## 🎯 결론
이 실습을 통해 Docker의 기본 개념, 포트 매핑, 바인드 마운트, 볼륨 관리, 로그 모니터링 등 DevOps의 핵심 기술을 습득했습니다.
