# gotgan-gitops

Argo CD가 바라보는 **GitOps 단일 진실 공급원**. gotgan 앱(backend·web)의 배포 매니페스트.

## 구조
```
bootstrap/root.yaml   app-of-apps 루트 (argocd/ 동기화)
argocd/               Argo CD Application (backend, web)
apps/backend/         backend Deployment/Service/Ingress + kustomization(이미지 태그)
apps/web/             web Deployment/Service/Ingress + kustomization(이미지 태그)
```

## 흐름
1. `dumbbelloper/toss` push → Jenkins가 backend·web 이미지 빌드(Kaniko) → `harbor.gotgan.live/gotgan/*` push
2. Jenkins가 `apps/*/kustomization.yaml`의 `newTag`를 git sha로 갱신·커밋
3. Argo CD가 변경 감지 → `gotgan` 네임스페이스에 자동 배포

## 호스트
- `app.gotgan.live` → web (React)
- `api.gotgan.live` → backend (Spring BFF + resource-server)
- 인증: `auth.gotgan.live` realm `gotgan` (client `gotgan-web-bff`)

## 의존(클러스터 사전 구성)
- 네임스페이스 `gotgan`, CNPG `pg-app`(secret `pg-app-app`), `harbor-pull`(imagePullSecret), `gotgan-backend-oidc`(web-bff secret)
