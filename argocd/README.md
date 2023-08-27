# Document
```
https://argo-cd.readthedocs.io/en/stable/getting_started/
https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/
```

# Setup
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

```
If we are using loadbalancer on k8s cluster, we can update current svc with this type
```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```
Access to cluster by IP of LB
```
kubectl get svc -n argocd
NAME                                      TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)                      AGE
argocd-applicationset-controller          ClusterIP      10.106.6.101     <none>          7000/TCP,8080/TCP            33d
argocd-dex-server                         ClusterIP      10.97.11.179     <none>          5556/TCP,5557/TCP,5558/TCP   33d
argocd-metrics                            ClusterIP      10.105.185.243   <none>          8082/TCP                     33d
argocd-notifications-controller-metrics   ClusterIP      10.97.113.12     <none>          9001/TCP                     33d
argocd-redis                              ClusterIP      10.108.119.40    <none>          6379/TCP                     33d
argocd-repo-server                        ClusterIP      10.99.239.222    <none>          8081/TCP,8084/TCP            33d
argocd-server                             LoadBalancer   10.101.116.174   192.168.1.242   80:32642/TCP,443:30585/TCP   33d
argocd-server-metrics                     ClusterIP      10.107.61.236    <none>          8083/TCP                     33d

```
Access Admin UI http://192.168.1.242
```
Username: admin
Password: kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```


# Add application to argocd
We have two example file to deploy redis , wordpress. We apply redis.yaml to create application-redis on argocd UI.

```
kubectl apply -f application-redis.yaml
```

# Enable metric for monitor argocd. 
Document here https://argo-cd.readthedocs.io/en/stable/operator-manual/metrics/

```
lvthao@lvthao:~/k8s-doc$ kubectl apply -f argocd/monitor/prometheus/service-monitor.yaml
servicemonitor.monitoring.coreos.com/argocd-metrics created
servicemonitor.monitoring.coreos.com/argocd-server-metrics created
servicemonitor.monitoring.coreos.com/argocd-repo-server-metrics created
servicemonitor.monitoring.coreos.com/argocd-applicationset-controller-metrics created
```