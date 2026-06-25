# 곳간 로그인 테마를 포함한 커스텀 Keycloak 이미지 (operator가 --optimized 로 기동 → kc.sh build 필수).
# 빌드: harbor.gotgan.live/gotgan/keycloak:26.5.0-gotgan1
# Keycloak CR(keycloak.yaml)의 spec.image 로 지정하고 realm loginTheme=gotgan 설정.
FROM quay.io/keycloak/keycloak:26.5.0
COPY themes/gotgan /opt/keycloak/themes/gotgan
# spec.image 사용 시 operator가 --optimized 를 붙이므로 빌드 단계 필요(런타임 옵션은 operator가 주입)
RUN /opt/keycloak/bin/kc.sh build --db=postgres
