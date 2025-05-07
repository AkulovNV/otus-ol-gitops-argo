# Apply ones
k apply -f applications/ingress-nginx/Application.yaml -n argocd

argocd app list|awk '{print $1,$6}'
argocd app get ingress-nginx

# Fix syncOptions
argocd app delete ingress-nginx
k apply -f applications/ingress-nginx/Application.yaml -n argocd
argocd app get ingress-nginx
git add .
git commit -am "fix" && git push origin main

# Try to scale up 
k scale deploy ingress-nginx-controller -n ingress-nginx --replicas=2

# Что будет если удалить приложение через kubectl ?
k delete -f applications/ingress-nginx/Application.yaml -n argocd