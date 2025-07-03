# Apply ones
k apply -f ApplicationSet.yaml -n argocd

argocd app list|awk '{print $1,$6}'
argocd app get ingress-nginx-dev
argocd app get ingress-nginx-prod

# Try to scale up via kuebctl
k scale deploy ingress-nginx-controller -n ingress-nginx --replicas=2

# Try to scale up via argocd
k apply -f ApplicationSet.yaml -n argocd

# Что будет если удалить приложение через kubectl ?
k delete -f ApplicationSet.yaml -n argocd