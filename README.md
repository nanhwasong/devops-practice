# DevOps 워크스테이션 구축 실습

## 프로젝트 개요
Windows 11 환경에서 DevOps 개발 환경을 구축하는 실습 프로젝트입니다.

## 설치된 도구
| 도구 | 버전 | 용도 |
|------|------|------|
| VS Code | v1.137.0 | 코드 편집기 |
| Git | v2.56.0 | 버전 관리 |
| Docker Desktop | v29.9.2 | 컨테이너 관리 |

## 프로젝트 구조
```
devops-practice/
├── docker/       # Docker 관련 파일
├── docs/         # 문서
├── scripts/      # 자동화 스크립트
└── README.md     # 프로젝트 설명
```

## 환경 구성
- **OS**: Windows 11
- **가상화**: WSL2 + Docker Desktop
- **버전 관리**: Git + GitHub

## 볼륨 영속성 검증 완료

- 볼륨 생성: `docker volume create my-volume`
- 컨테이너 연결: `-v my-volume:/usr/share/nginx/html`
- 파일 복사: `docker cp app/index.html my-nginx-volume:/usr/share/nginx/html/`
- 영속성 확인: 컨테이너 삭제 후 재생성해도 데이터 유지 ✅