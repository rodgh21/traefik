# Traefik to SubChart
https://coffeewhale.com/k8s/helm/2019/06/05/helm-chart/

# Traefik to Your Own Chart
1. 아래 가이드를 따라서 traefik helm chart를 생성한다.

https://github.com/traefik/traefik-helm-chart/blob/v34.1.0/EXAMPLES.md#use-this-chart-as-a-dependency-of-your-own-chart
에서 발췌,,,
Use this Chart as a dependency of your own chart
```bash
# traefik repo 등록
helm repo add traefik https://traefik.github.io/charts
```


First, let's create a default Helm Chart, with Traefik as a dependency.
```bash
helm create foo
cd foo
echo "
dependencies:
  - name: traefik
    version: "34.1.0"
    repository: "https://traefik.github.io/charts"
" >> Chart.yaml
```

Second, let's tune some values like enabling HPA:

```bash
cat <<-EOF >> values.yaml
traefik:
  autoscaling:
    enabled: true
    maxReplicas: 3
EOF
```

Third, one can see if it works as expected:
```bash
helm dependency update
helm dependency build
helm template . | grep -A 14 -B 3 Horizontal
```

It should produce this output:

```yaml

2. 생성된 chart에서 charts/traefik-${version}.tgz 파일만 별도위치에 저장해둔다.
```bash
mv charts/traefik-34.1.0.tgz ~/Desktop
```

3. 다시 새로운 helm Chart를 만들고 dependency 설정을 한다.
```bash
#신규 Chart 생성
helm create traefik
#template 삭제
rm -rf template
#2에서 저장한 tgz 이동
mv ~/Desktop/traefik-34.1.0.tgz ./charts/
#Chart 수정
apiVersion: v2
name: traefik
version: 1.0.0
dependencies:
  - name: traefik
    version: 34.1.0
    repository: https://traefik.github.io/charts
```
---
# Source: foo/charts/traefik/templates/hpa.yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: release-name-traefik
  namespace: flux-system
  labels:
    app.kubernetes.io/name: traefik
    app.kubernetes.io/instance: release-name-flux-system
    helm.sh/chart: traefik-24.0.0
    app.kubernetes.io/managed-by: Helm
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: release-name-traefik
  maxReplicas: 3
```

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

