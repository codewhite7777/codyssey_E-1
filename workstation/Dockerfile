# 베이스 이미지: nginx의 경량 alpine 버전
FROM nginx:alpine

# 이미지 메타데이터
LABEL maintainer="codewhite7777"
LABEL description="Codyssey workstation mission - custom nginx"

# 환경 변수 설정 (커스텀 포인트 1: 실행 환경 구분)
ENV APP_ENV=development

# 정적 콘텐츠 복사 (커스텀 포인트 2: 기본 페이지를 내 콘텐츠로 교체)
COPY ./site/ /usr/share/nginx/html/

# 컨테이너가 사용하는 포트 선언
EXPOSE 80