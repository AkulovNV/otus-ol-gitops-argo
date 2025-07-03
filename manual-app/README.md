# This is a sample application for demonstrating GitOps with ArgoCD

# Port forward
kuebctl port-forward svc/argocd-server -n argocd 80:8080

# Get password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

argocd login localhost:8080 --username admin --password $(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d) --insecure

#argocd login localhost:8080 --username admin --password <password> --insecure

argocd app create nginx-app \
  --repo https://github.com/AkulovNV/otus-ol-gitops-argo.git \
  --path manual-app \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace nginx-app \
  --sync-policy automated \
  --self-heal \
  --sync-option CreateNamespace=true

# Check the status of the application
argocd app get nginx-app
kubectl get applications.argoproj.io -n argocd

# Change image: nginx:1.27 -> nginx:1.28
git push origin main

# Sync the application
argocd app sync nginx-app 

# Delete the application
argocd app delete nginx-app
