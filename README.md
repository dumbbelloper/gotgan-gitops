# gotgan-gitops

곳간(gotgan) 플랫폼의 **단일 IaC 저장소**. 인프라(platform) + 앱 배포(apps)를 한 곳에서 버전관리한다.

## 구조
```
platform/     클러스터 인프라(IaC): cert-manager, CNPG, Keycloak(+곳간 테마), Harbor/Nexus, Jenkins, Argo CD, Cloudflare
              → helm/kubectl 명령형 부트스트랩 (platform/README.md)
apps/         앱 워크로드 — Argo CD가 동기화
  backend/    Spring Boot (api.gotgan.live), CNPG pg-app, live 프로파일
  web/        React/nginx (app.gotgan.live), same-origin BFF 리버스프록시
argocd/       Argo CD Application (backend, web)
bootstrap/    app-of-apps 루트
```

## 아키텍처 한눈에
```
Git push(dumbbelloper/toss) → Jenkins(Kaniko) → Harbor 이미지 + 태그 갱신(이 레포)
                                                       ↓
외부 → Cloudflare(공인TLS) → Tunnel → ingress-nginx → Argo CD 자동배포 워크로드
  app.gotgan.live(web) ─BFF프록시→ backend ─→ CNPG pg-app
  로그인: auth.gotgan.live (Keycloak)  ·  realm gotgan(앱) / platform(ops SSO)
  레지스트리 harbor./nexus.  ·  CI jenkins.  ·  CD argocd.   (모두 Keycloak SSO)
```

## 비밀 원칙
커밋 금지. k8s Secret(클러스터 직접 생성) 또는 `__PLACEHOLDER__`(적용 직전 치환). 자격증명 요약=호스트 `~/info.md`.
추후 **Sealed Secrets/External Secrets** 도입 시 암호화하여 git 관리 예정.
