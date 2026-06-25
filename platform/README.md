# platform — gotgan 클러스터 인프라(IaC)

bare-metal k8s(4노드, Cilium) 위 플랫폼 컴포넌트. **부트스트랩은 helm/kubectl 명령형**으로 설치하고,
앱(backend/web)은 Argo CD GitOps(`../apps`, `../argocd`)로 관리한다.

> ⚠️ **비밀은 커밋 안 함**(`.gitignore`). 값이 `__XXX__` 플레이스홀더인 파일은 적용 직전 실제 값으로 치환하거나,
> 비밀은 k8s Secret으로 클러스터에 직접 생성한다. 자격증명 요약은 호스트 `~/info.md`.

## 설치 순서 (의존성 기준)

| # | 컴포넌트 | 명령 |
|---|---------|------|
| 00 | cert-manager + 내부 CA | `helm upgrade --install cert-manager jetstack/cert-manager -n cert-manager --create-namespace --set crds.enabled=true` → `kubectl apply -f 00-cert-manager/clusterissuer.yaml` |
| 10 | CloudNativePG | `helm upgrade --install cnpg cnpg/cloudnative-pg -n cnpg-system --create-namespace` → `kubectl apply -f 10-cnpg/` |
| 20 | Keycloak Operator + CR | operator: keycloak-k8s-resources 26.5.0 → 커스텀 이미지 빌드(Dockerfile, 곳간 테마) → `kubectl apply -f 20-keycloak/keycloak.yaml` → realm import(JSON, 비밀 치환 후) |
| 30 | Harbor + Nexus | `helm upgrade --install harbor harbor/harbor -n harbor -f 30-registry/harbor-values.yaml` · `kubectl apply -f 30-registry/nexus.yaml` |
| 40 | Jenkins | `helm upgrade --install jenkins jenkins/jenkins -n jenkins -f 40-jenkins/jenkins-values.yaml` |
| 50 | Argo CD | `helm upgrade --install argocd argo/argo-cd -n argocd -f 50-argocd/argocd-values.yaml`(시크릿 치환) |
| 60 | Cloudflare Tunnel | 호스트 마스터노드 cloudflared systemd(토큰형). 라우팅·DNS는 CF API. `60-cloudflare/README.md` |

## 비밀 주입 위치
- 운영 앱 시크릿(toss/telegram): k8s Secret `gotgan-backend-secrets`(env), `../apps/backend` 가 envFrom.
- 플랫폼 OIDC client secret: Keycloak `platform` realm. Argo=argocd-values(치환)·Harbor=API·Jenkins=JCasC/콘솔.
- Harbor robot(CI push), imagePullSecret `harbor-pull`, backend OIDC `gotgan-backend-oidc` 등은 클러스터에 직접 생성.

## 도메인 / 네트워크
- 외부: Cloudflare(공인 TLS) → Tunnel → ingress-nginx(LB `192.168.56.200`, MetalLB) → 서비스
- 내부 TLS: cert-manager `gotgan-ca-issuer`. 서브도메인: app/api/auth/argocd/harbor/nexus/jenkins`.gotgan.live`
