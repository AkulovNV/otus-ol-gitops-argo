Add cluster to ArgoCD

```bash
kubectl apply -n kube-system -f token.yaml 
kubectl apply -n kube-system -f sa.yaml
kubectl apply -n kube-system -f cr.yaml 
kubectl apply -n kube-system -f crb.yaml 
```

export secret_name=$(kubectl -n kube-system get sa argocd-manager -o go-template='{{range .secrets}}{{.name}}{{"\n"}}{{end}}')
export ca_crt=$(kubectl -n kube-system get secrets $secret_name -o go-template='{{index .data "ca.crt"}}')
export token=$(kubectl -n kube-system get secrets $secret_name -o go-template='{{.data.token}}' | base64 -d)

echo $ca_crt
echo $token