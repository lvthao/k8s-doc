# Document
```
Document to install monitoring stack on K8s 
```

#  metric servers
## Install
Metric server support k8s 1.27
```
 k apply -f metric-server/components.yaml

```
## Usage
```
k top pod

k top node

```

#  prometheus
## Install
```

```
## Usage
```
```

#  Loki
## Install
```
helm install --values loki/values.yaml loki grafana/loki-stack -n loki
```

## Usage
If we are using loadbalancer on k8s cluster, we can update current svc with this type
```
kubectl patch svc loki-grafana -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
root@lvthao:~# k get svc -n loki
NAME              TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
loki              ClusterIP      10.105.74.229   <none>          3100/TCP       57m
loki-grafana      LoadBalancer   10.107.8.215    192.168.1.244   80:31775/TCP   57m
loki-headless     ClusterIP      None            <none>          3100/TCP       57m
loki-memberlist   ClusterIP      None            <none>          7946/TCP       57m

```


Access Admin UI http://192.168.1.242
```
Username: admin
Password:  k get secrets loki-grafana -n loki  -o jsonpath="{.data.admin-password}"| base64 -d
GO log : http://192.168.1.244/explore?schemaVersion=1&panes=%7B%22y0K%22%3A%7B%22datasource%22%3A%22P8E80F9AEF21F6940%22%2C%22queries%22%3A%5B%7B%22refId%22%3A%22A%22%2C%22expr%22%3A%22%22%2C%22queryType%22%3A%22range%22%2C%22datasource%22%3A%7B%22type%22%3A%22loki%22%2C%22uid%22%3A%22P8E80F9AEF21F6940%22%7D%7D%5D%2C%22range%22%3A%7B%22from%22%3A%22now-1h%22%2C%22to%22%3A%22now%22%7D%7D%7D&orgId=1
```
