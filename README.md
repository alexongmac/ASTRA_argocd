### Helm Installation with argocd

Once the EKS is launch, here's the steps as follows:

- aws eks update-kubeconfig --region us-east-1 --name astra-eks-development-cluster
- kubectl create namespace argocd

follow by the installation of helm chart accordingly: copy and paste each helm repo to terminal and "enter"

helm repo add eks https://aws.github.io/eks-charts

helm repo add traefik https://traefik.github.io/charts

helm search repo traefik/traefik --versions | head

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo add grafana https://grafana.github.io/helm-charts

helm repo add autoscaler https://kubernetes.github.io/autoscaler

helm repo add kyverno https://kyverno.github.io/kyverno/

helm repo add aqua https://aquasecurity.github.io/helm-charts/

helm repo update

### To check the helm app version for input to version

helm search repo eks/aws-load-balancer-controller --versions | head -20

helm search repo traefik/traefik --versions | head -20

helm search repo prometheus-community/kube-prometheus-stack --versions | head -20

helm search repo autoscaler/cluster-autoscaler --versions | head -20

helm search repo grafana/loki --versions | head -15

helm search repo kyverno/kyverno --versions | head -20

helm search repo aqua/trivy-operator --versions | head -10

### Get Grafana 'admin' user password by running:

kubectl --namespace monitoring get secrets kube-prometheus-stack-grafana -o jsonpath="{.data.admin-password}" | base64 -d ; echo

### Wait for 5 mins before launching https://astra-prom.sctp-sandbox.com and https://astra-grafana.sctp-sandbox.com
