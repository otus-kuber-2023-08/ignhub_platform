### apply nginx with ConfigMap + servise
k apply -f nginx.yaml

### install prometheus
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update prometheus-community

helm show values prometheus-community/kube-prometheus-stack > prom-values.yaml
Grafana values:
https://github.com/grafana/helm-charts/blob/main/charts/grafana/values.yaml
adminPassword: prom-operator (for admin)

helm upgrade --install prom-stack prometheus-community/kube-prometheus-stack --wait \
--namespace=default \
--create-namespace \
-f values.yaml

### create monitor
k apply -f service-monitor.yaml

metadata:
   labels:
    release: prom-stack
or
prometheus:
  prometheusSpec:
   serviceMonitorSelectorNilUseHelmValues: false

### debug prometheus on port 9090
in /targets must be our ServiceMonitor

