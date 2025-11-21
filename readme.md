How to setup for e-commerce 

1. Install Ingress Controller
``` bash 
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress --create-namespace
```
2. Install cert-manager
```bash 
helm repo add jetstack https://charts.jetstack.io
helm repo update

helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --set installCRDs=true
```
3. Install ArgoCD
```bash 
kubectl create namespace argocd

kubectl apply -n argocd -f \
https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
- Get argocd password `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`
- login `argocd login <server address> --username admin --password <password>`
- add your application repo `argocd repo add <git-repo-url> --username <git-user> --password <git-pass>`


4. Install K8s Dashboard
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```
5. Install monitoring stack
    - prometheus
    ```bash
    kubectl create namespace monitoring
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm repo update
    helm install prometheus prometheus-community/prometheus \
     --namespace monitoring
    ```
    - grafana
    ```bash
    helm repo add grafana https://grafana.github.io/helm-charts
    helm repo update
    helm install grafana grafana/grafana \
      --namespace monitoring
    ```
    - istio
    ```bash
    istioctl install --set profile=demo -y
    ```
    - kiali
    ```bash
    helm repo add kiali https://kiali.org/helm-charts
    helm repo update
    
    helm install kiali-server kiali/kiali-server \
      --namespace istio-system \
      --set auth.strategy="anonymous"
    ```
    - jaeger
    ```bash
    kubectl create namespace observability
    kubectl label ns observability istio-injection=disabled
    kubectl apply -n observability -f https://github.com/jaegertracing/jaeger-operator/releases/download/v1.60.0/jaeger-operator.yaml  
    ```
    deploy jaeger yaml (a custome resource)

6. Install certificate manager
`kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.14.0/cert-manager.yaml `
7. create clusterIssuer (yaml file created, just apply with kubectl)
8. Apply ingress (all ingress) for the services to be accessed from public
9. cd into cloned argocd repo (or where you have the manifests), apply the manifests

Write Helm chart

No ingress

Service type = ClusterIP

8. For frontend:

Helm chart with ingress

Public endpoint