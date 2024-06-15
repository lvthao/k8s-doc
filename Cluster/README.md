# Install Cluster k8s
```
Document to install K8s with kubeadm , gke and eks
```

# Setup cluster K8s with KubeAdm
## Setup on mater node
### Add repo
```
swapoff -a

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

mkidr /etc/apt/keyrings/
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.27/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.27/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

apt-get update

```
### Add kernel module 
```

sudo modprobe overlay
sudo modprobe br_netfilter

sudo tee /etc/sysctl.d/kubernetes.conf<<EOF
   net.bridge.bridge-nf-call-ip6tables = 1
   net.bridge.bridge-nf-call-iptables = 1
   net.ipv4.ip_forward = 1
   EOF

sudo sysctl --system
  
```
### Install package container 
```
sudo apt-get remove containerd 
  
sudo apt-get update
  
sudo apt-get install containerd.io -y
   
sudo containerd config default | sudo tee /etc/containerd/config.toml

sudo systemctl restart containerd

sudo systemctl enable containerd

```
### Install package kubeadm 
```
sudo apt-get install kubelet=1.27.15-1.1 kubeadm=1.27.15-1.1 kubectl=1.27.15-1.1
sudo apt-mark hold containerd kubelet kubeadm kubectl
   
sudo systemctl enable kubelet

```
### Init cluster 
```
kubeadm config images pull --image-repository=registry.k8s.io --cri-socket unix:///run/containerd/containerd.sock --kubernetes-version 1.27.15

kubeadm init --pod-network-cidr=10.244.0.0/16 --cri-socket unix:///run/containerd/containerd.sock --kubernetes-version 1.27.15 --ignore-preflight-errors=NumCPU

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:
kubeadm join 192.168.1.101:6443 --token xxxx \
        --discovery-token-ca-cert-hash xxxx

```
### update kubeconfig
```
mkdir -p $HOME/.kube

cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

sudo chown $(id -u):$(id -g) $HOME/.kube/config

``` 
### Add network 
```
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
```

## Setup on worker node
### Add repo
```
swapoff -a

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

mkidr /etc/apt/keyrings/
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.27/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.27/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
apt-get update

```
### Add kernel module 
```

sudo modprobe overlay
sudo modprobe br_netfilter

sudo tee /etc/sysctl.d/kubernetes.conf<<EOF
   net.bridge.bridge-nf-call-ip6tables = 1
   net.bridge.bridge-nf-call-iptables = 1
   net.ipv4.ip_forward = 1
   EOF

sudo sysctl --system
  
```
### Install package container 
```
sudo apt-get remove containerd 
  
sudo apt-get update
  
sudo apt-get install containerd.io -y
   
sudo containerd config default | sudo tee /etc/containerd/config.toml

sudo systemctl restart containerd

sudo systemctl enable containerd

```
### Install package kubeadm 
```
sudo apt-get install kubelet=1.27.15-1.1 kubeadm=1.27.15-1.1 kubectl=1.27.15-1.1
sudo apt-mark hold containerd kubelet kubeadm kubectl
   
sudo systemctl enable kubelet

```
### Join worker 
```
kubeadm join <MASTER_NODE_IP>:6443 --token xxxxxxxxxxxxxxx --discovery-token-ca-cert-hash sha256:xxxxxxxxxxxxxxxxxxxxxxxx
```