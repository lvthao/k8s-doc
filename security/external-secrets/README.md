# Introduction
External Secrets Operator is a Kubernetes operator that integrates external secret management (AWS Secrets Manager, HashiCorp Vault, Google Secrets Manager, Azure Key Vault, IBM Cloud Secrets Manager, CyberArk Conjur )  instead create and store on the etcd service.
# Install external secret on k8s 
```
helm repo add external-secrets https://charts.external-secrets.io

helm install external-secrets \
   external-secrets/external-secrets \
    -n external-secrets \
    --create-namespace \
```
# Setup secret with GCP
Document: https://external-secrets.io/latest/provider/google-secrets-manager/#gcp-service-account-authentication
Generate SA from goolge console, grant secret manager permission  and create resource on k8s by copmmand below.
```
 k create secret generic gcpsm-secret -n external-secrets --from-file sa-example.json
```
## Update secret store
```
 k apply -f gcpsm.yaml
``` 
## Creating external secret
```
 k apply -f credential-test.yaml
```
