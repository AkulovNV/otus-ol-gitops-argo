# This is a sample application for demonstrating GitOps with ArgoCD
k port-forward svc/argocd-server -n argocd 8080:80
# k -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
# argocd login localhost:8080 --username admin --password <password> --insecure

```bash
# Login to ArgoCD
argocd login localhost:8080 --username admin --password $(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d) --insecure
```

# Create via CLI
```bash 
argocd app create app \
  --repo https://github.com/AkulovNV/otus-ol-gitops-argo.git \
  --path applications/app/resources \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace app \
  --sync-policy automated \
  --self-heal \
  --sync-option CreateNamespace=true
```

# Check the status of the application
argocd app get app
k get applications.argoproj.io -n argocd

# Change image: nginx:1.27 -> nginx:1.28
git push origin main

# Sync the application and check the image
argocd app sync app 
kgp -n app -o yaml|grep image

# Check logs
k port-forward svc/app-service -n app 8888:80
argocd app logs app

# Create application with Application
k apply -f Application.yaml -n argocd

# Fix syncOptions
argocd app get app2
argocd app terminate-op app2
argocd app delete app2 --yes --wait
k apply -f Application.yaml -n argocd