# Introduction 
Document here : https://kubernetes.io/docs/tasks/administer-cluster/nodelocaldns/
# Install on k8s 
```
k apply -f nodelocaldns.yaml
```
# Test DNS query 

10.96.0.10: kube DNS svc 
```
kubectl apply -f https://k8s.io/examples/admin/dns/dnsutils.yaml
kubectl exec -it  dnsutils bash

root@dnsutils:/# time dig +short @169.254.0.0 www.google.com
142.251.220.36

real    0m0.019s
user    0m0.009s
sys     0m0.010s
root@dnsutils:/# time dig +short @10.96.0.10 www.google.com
142.251.222.196

real    0m0.064s
user    0m0.015s
sys     0m0.006s

```
