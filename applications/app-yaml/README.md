# This is a sample application for demonstrating GitOps with ArgoCD
k port-forward svc/argocd-server -n argocd 80:8080
k -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
argocd login localhost:8080 --username admin --password <password> --insecure

argocd app create nginx-app \
  --repo https://github.com/AkulovNV/otus-ol-gitops-argo.git \
  --path applications/app-yaml \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace nginx-app \
  --sync-policy automated \
  --self-heal \
  --sync-option CreateNamespace=true

# Check the status of the application
argocd app get nginx-app
k get applications.argoproj.io -n argocd

# Change image: nginx:1.27 -> nginx:1.28
git push origin main

# Sync the application
argocd app sync nginx-app 

# Check logs
k port-forward pod/nginx-app -n nginx-app 8888:80
argocd app logs nginx-app