# nginx 공식 이미지 사용
FROM nginx:alpine

# 작업 디렉토리 설정
WORKDIR /usr/share/nginx/html

# index.html 복사
COPY app/index.html .

# 80번 포트 노출
EXPOSE 80