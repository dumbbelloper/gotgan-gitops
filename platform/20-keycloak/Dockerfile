# 곳간 로그인 테마를 포함한 커스텀 Keycloak 이미지.
# 빌드: harbor.gotgan.live/gotgan/keycloak:26.5.0-gotgan
# Keycloak CR(keycloak.yaml)의 spec.image 로 지정하고 realm loginTheme=gotgan 설정.
FROM quay.io/keycloak/keycloak:26.5.0
COPY themes/gotgan /opt/keycloak/themes/gotgan
