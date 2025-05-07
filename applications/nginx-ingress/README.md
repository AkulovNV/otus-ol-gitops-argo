# Apply ones
k apply -f applications/nginx-ingress/Application.yaml -n argocd

argocd app list|awk '{print $1,$6}'
argocd app get nginx-ingress

# Fix syncOptions
git push origin main
argocd app delete nginx-ingress
k apply -f applications/nginx-ingress/Application.yaml -n argocd
argocd app get nginx-ingress