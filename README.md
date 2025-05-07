Install ArgoCD

```bash
helm repo add argo-cd https://argoproj.github.io/argo-helm
helm repo update
helm upgrade -i argocd argo-cd/argo -n argocd --create-namespace -f values.yaml
```
