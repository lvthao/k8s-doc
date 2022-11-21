# Lab HPA on k8s
```
kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
kubectl get hpa php-apache --watch
```
- Document:
  - https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/