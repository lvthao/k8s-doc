# RBAC
## Grant permission to user access on k8s:
We will create a demoUser and grant this user a permission to readonly on pod 
1/ Create user demouser 
* Generate the ssl certificate for demoUser
```
openssl genrsa -out demoUser.key 2048
openssl req -new -key demoUser.key -out demoUser.csr 
```

2/ Create crs and submit csr request of demoUser

* Get the csr file content 
```
CSR=$(cat demoUser.csr | base64 | tr -d "\n")
```
* Create CSR request 
```
cat <<EOF | kubectl apply -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: demoUser
spec:
  groups:
  - system:authenticated
  request: $CSR
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
EOF
```
* list of csr: 
```
kubectl get csr
```
*Approve the demoUser CSR
```
kubectl certificate approve demoUser
```

3/ get the certification and create role/rolebinding
```
kubectl get csr demoUser -o jsonpath='{.status.certificate}'| base64 -d > demoUser.crt
```
#Create the role for developer
```
kubectl create role developer --verb=create --verb=get --verb=list --verb=update --verb=delete --resource=pods
```
#Create rolebinding for developer
```
kubectl create rolebinding developer-binding-demoUser --role=developer --user=demoUser
```

#Test with the demo user 
```
k get pod --as demoUser
k get deployment --as demoUser
```