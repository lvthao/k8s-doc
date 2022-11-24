# Lab wordpress nodeport on k8s
```
mkdir /tmp/data/pv-1
mkdir /tmp/data/pv-2
kubectl create secret generic mysql-pass --from-literal=password=123456
kubectl apply -f local-volume.yaml
kubectl apply -f mysql-deployment.yaml

kubectl apply -f worldpress-deployment.yaml
```
- Document:
  - https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/