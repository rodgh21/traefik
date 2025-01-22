# Traefik 23.0.1

```bash
# publishedService 활성화
kubernetesIngress.publishedService.enabled=true
kubernetesIngress.publishedService.pathOverride="traefik/traefik"
# logs 활성화
logs.access.enabled=true
# Upgrade Traefik
helm upgrade traefik traefik/traefik
```

## Port-forwarding
테스트를 위해 traefik:9000 open
```bash
ports.traefik.expose=true
```

## TODO
9000포트 개방없이 dashboard 접근이 가능한가?

# Traefik 34.1.0 (2025-01-22)
1. [x] Chart 23.0.1 -> 34.1.0
2. [x] App version v2.10.0 -> v3.3.2

## TODO
3. kubernetesIngress.publishedService.pathOverride="traefik/traefik" 적용없이 테스트 진행 중
4. create a secure ingress for 9000 port

