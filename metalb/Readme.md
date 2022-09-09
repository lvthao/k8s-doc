# Lab install MetalLB on K8s onpremise
## Requirements:
- K8s cluster already has more than one worker
- K8s version more than 1.13.0
- Document:
  - https://metallb.universe.tf/#requirements
##Step 1: Install metallb 
### Download file setup yaml 
```
root@lvthao:~/lab# mkdir metalb
root@lvthao:~/lab# cd metalb/
root@lvthao:~/lab/metalb# wget https://raw.githubusercontent.com/metallb/metallb/v0.13.5/config/manifests/metallb-native.yaml
```
### Install yaml 
```
root@lvthao:~/lab/metalb# k apply -f metallb-native.yaml
namespace/metallb-system created
customresourcedefinition.apiextensions.k8s.io/addresspools.metallb.io created
customresourcedefinition.apiextensions.k8s.io/bfdprofiles.metallb.io created
customresourcedefinition.apiextensions.k8s.io/bgpadvertisements.metallb.io created
customresourcedefinition.apiextensions.k8s.io/bgppeers.metallb.io created
customresourcedefinition.apiextensions.k8s.io/communities.metallb.io created
customresourcedefinition.apiextensions.k8s.io/ipaddresspools.metallb.io created
customresourcedefinition.apiextensions.k8s.io/l2advertisements.metallb.io created
serviceaccount/controller created
serviceaccount/speaker created
role.rbac.authorization.k8s.io/controller created
role.rbac.authorization.k8s.io/pod-lister created
clusterrole.rbac.authorization.k8s.io/metallb-system:controller created
clusterrole.rbac.authorization.k8s.io/metallb-system:speaker created
rolebinding.rbac.authorization.k8s.io/controller created
rolebinding.rbac.authorization.k8s.io/pod-lister created
clusterrolebinding.rbac.authorization.k8s.io/metallb-system:controller created
clusterrolebinding.rbac.authorization.k8s.io/metallb-system:speaker created
secret/webhook-server-cert created
service/webhook-service created
deployment.apps/controller created
daemonset.apps/speaker created
validatingwebhookconfiguration.admissionregistration.k8s.io/metallb-webhook-configuration created
```

## Step2:  Setup metalb config. 

We will setup layer2 configuration. This will create a LB pool which will assign the IP of loadbalancer to the service.  https://metallb.universe.tf/configuration/
```
root@lvthao:~/lab/metalb#  touch metallb-config.yaml

apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: metallb-pool
  namespace: metallb-system
spec:
  addresses:
  - 192.168.1.240-192.168.1.250
---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: example
  namespace: metallb-system
spec:
  ipAddressPools:
  - metallb-pool
```

##Step3: Test with loadbalancing service
```
apiVersion: v1
kind: Namespace
metadata:
  name: kube-demo-lb
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kube-demo-lb
  namespace: kube-demo-lb
  labels:
    app: kube-demo-lb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kube-demo-lb
  template:
    metadata:
      labels:
        app: kube-demo-lb
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: kube-demo-lb
  namespace: kube-demo-lb
spec:
  selector:
    app: kube-demo-lb
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

##Step4: Verify LB
```
root@lvthao:~/lab/metalb# k get svc -n  kube-demo-lb
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
kube-demo-lb   LoadBalancer   10.105.110.66   192.168.1.241   80:30266/TCP   22m
```

```

root@lvthao:~/lab/metalb# curl -I 192.168.1.241:80
HTTP/1.1 200 OK
Server: nginx/1.14.2
Date: Fri, 09 Sep 2022 14:44:15 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Tue, 04 Dec 2018 14:44:49 GMT
Connection: keep-alive
ETag: "5c0692e1-264"
Accept-Ranges: bytes
```
