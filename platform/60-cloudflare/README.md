# Cloudflare Tunnel

외부 노출 = Cloudflare(공인 TLS, Universal SSL) → Tunnel → ingress-nginx.

- 커넥터: **호스트 마스터노드**에 `cloudflared` systemd(토큰형, 원격관리 터널). `--token eyJ...`.
- 라우팅(Public Hostname)·DNS는 Cloudflare API/대시보드에서 관리(로컬 config 아님).
- 7개 서브도메인(auth/app/api/argocd/harbor/nexus/jenkins`.gotgan.live`) → `https://192.168.56.200:443`
  (originRequest `noTLSVerify: true` — 내부는 cert-manager self-signed).
- proxied CNAME → `<tunnel-id>.cfargotunnel.com`.

> CF API 토큰은 커밋 금지. 필요 스코프: Account>Cloudflare Tunnel>Edit, Zone>DNS>Edit, Zone>Zone>Read.
