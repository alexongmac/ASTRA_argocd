### The actual launch order

1. terraform apply → VPC, EKS, nodes, cert, WAF, IAM, pod identity
2. kubectl apply -k bootstrap/argocd-install/ → ArgoCD
3. kubectl apply bootstrap/astra-project.yaml → AppProject
   kubectl apply bootstrap/traefik-app-ENV.yaml → Traefik + IngressClass
   kubectl apply bootstrap/root-app-ENV.yaml → Applications/
   kubectl apply bootstrap/root-app-ENV-env.yaml → Applications/ENV/
4. ArgoCD syncs web-app-ENV → reads envs/ENV/web-app/ → app runs

### Helm Installation with argocd

Once the EKS is launch, here's the steps as follows:

- aws eks update-kubeconfig --region us-east-1 --name astra-eks-development-cluster
- kubectl create namespace argocd

### To check the helm app version for input to version.

helm search repo eks/aws-load-balancer-controller --versions | head -20

helm search repo traefik/traefik --versions | head -20

helm search repo prometheus-community/kube-prometheus-stack --versions | head -20

helm search repo autoscaler/cluster-autoscaler --versions | head -20

helm search repo grafana/loki --versions | head -15

helm search repo kyverno/kyverno --versions | head -20

helm search repo aqua/trivy-operator --versions | head -10

### Get Grafana 'admin' user password by running:

kubectl --namespace monitoring get secrets kube-prometheus-stack-grafana -o jsonpath="{.data.admin-password}" | base64 -d ; echo

### Get ArgoCD 'admin' user password by running

kubectl get secret -n argocd

kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode

### Wait for 5 mins before launching as follows:

https://astra-prom.sctp-sandbox.com
https://astra-grafana.sctp-sandbox.com
https://alertmanager.sctp-sandbox.com
https://astra-argcd.sctp-sandbox.com

### you can query at prometheus as follows:

- request rate by route
  sum by (service) (rate(traefik_service_requests_total[5m]))

- error rate
  sum(rate(traefik_service_requests_total{code=~"5.."}[5m]))
  / sum(rate(traefik_service_requests_total[5m]))
- p95 latency per service

histogram_quantile(0.95, sum by (service,le) (rate(traefik_service_request_duration_seconds_bucket[5m])))

### Import Grafana dashboard 17346 (official Traefik v3) — instant visualisation, no custom work.

Dashboards → Import → paste `17346` → select Prometheus.
