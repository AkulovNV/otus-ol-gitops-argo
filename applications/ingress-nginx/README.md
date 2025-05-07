# Apply ones
k apply -f applications/ingress-nginx/Application.yaml -n argocd

argocd app list|awk '{print $1,$6}'
argocd app get ingress-nginx

# Fix syncOptions
git add .
git commit -am "fix" && git push origin main
argocd app delete ingress-nginx
k apply -f applications/ingress-nginx/Application.yaml -n argocd
argocd app get ingress-nginx