Install ArgoCD

```bash
helm repo add argo-cd https://argoproj.github.io/argo-helm
helm repo update
helm upgrade -i argocd argo-cd/argo-cd -n argocd --create-namespace -f values.yaml
```

Install Vault

```bash
helm repo add hashicorp https://helm.releases.hashicorp.com
helm repo update

helm install vault hashicorp/vault \
  --namespace vault --create-namespace \
  --set "server.dev.enabled=true"

kubectl port-forward svc/vault -n vault 8200:8200

helm upgrade -i argocd argo-cd/argo-cd -n argocd --create-namespace -f values.yaml -f values-avp.yaml


vault login -address=http://127.0.0.1:8200 -tls-skip-verify
vault kv put -address=http://127.0.0.1:8200 --tls-skip-verify secret/app username=admin password=cGFzc3dvcmQ=
vault auth enable kubernetes -address=http://127.0.0.1:8200 --tls-skip-verify

cat <<EOF > policy.hcl
# policy.hcl
path "secret/*" {
  capabilities = ["read"]
}
EOF

vault policy write argocd-policy policy.hcl

SA_NAME=argocd-repo-server
NAMESPACE=argocd
kubectl create token argocd-repo-server -n argocd

TOKEN_REVIEW_JWT=$(kubectl create token argocd-repo-server -n argocd)
KUBE_CA_CERT=$(kubectl config view --raw --minify --flatten -o jsonpath="{.clusters[0].cluster.certificate-authority-data}" | base64 -d)
KUBE_HOST=$(kubectl config view --raw --minify --flatten -o jsonpath="{.clusters[0].cluster.server}")

vault write -address=http://127.0.0.1:8200 -tls-skip-verify auth/kubernetes/config \
  token_reviewer_jwt="$VAULT_TOKEN" \
  kubernetes_host="$KUBE_HOST" \
  kubernetes_ca_cert="$KUBE_CA_CERT"

vault write -address=http://127.0.0.1:8200 -tls-skip-verify auth/kubernetes/role/argocd-role \
    bound_service_account_names=argocd-repo-server \
    bound_service_account_namespaces=argocd \
    policies=argocd-policy \
    ttl=48h
    
``` 