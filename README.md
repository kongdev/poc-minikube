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


## Create Argo Application
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