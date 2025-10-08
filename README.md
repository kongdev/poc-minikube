# POC Helm Node.js Application

## Docker Commands

Build and run the application locally:

```bash
docker build -t node-app:latest .
docker run -p 3000:3000 node-app:latest
```

## Helm Commands

Install the Helm chart:

```bash
helm install nodeapp ./nodeapp -n argocd
```

Uninstall the Helm chart:

```bash
helm uninstall nodeapp -n argocd
```

## Kubernetes Commands

Port forward to access the application:

```bash
kubectl port-forward svc/nodeapp 3003:3000 -n argocd
```

## Application Access

After deployment, the application will be available at:
- Local Docker: http://localhost:3000
- Kubernetes (port-forward): http://localhost:3003


## Install ArgoCD on Minikube 
```bash
kubectl create ns argocd &&\
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## ArgoCD server service to NodePort
```bash
kubectl patch svc argocd-server -n argocd -p '{"spec":{"type":"NodePort"}}'
service/argocd-server patched
```
## ArgoCD Get password
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo
```

## Create Argo Application Components
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: components
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/kongdev/poc-minikube
    path: manifest
    targetRevision: HEAD
    directory:
      recurse: true
      jsonnet: {}
  destination:
    name: in-cluster
    namespace: argocd
  syncPolicy:
    automated: {}
```



## Adding Bitnami Helm repository
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

## Updating Helm repositories
```bash 
helm repo update
```

## Helm Installing PostgreSQL
```bash
helm install postgresql bitnami/postgresql -n argocd
```

## Helm Uninstalling PostgreSQL
```bash
helm uninstall postgresql -n argocd
```

## Checking if PostgreSQL pod is running 
```bash
kubectl get pods -n argocd | grep postgresql
```

## forward PostgreSQL port 5442
```bash
kubectl port-forward --namespace argocd svc/postgresql 5442:5432
```

## Getting  PostgreSQL Default password from secret
```bash
export POSTGRES_PASSWORD=$(kubectl get secret --namespace argocd postgresql -o jsonpath="{.data.postgres-password}" | base64 -d) && echo "Password: $POSTGRES_PASSWORD"
```
