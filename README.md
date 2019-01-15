# What is need?
Create a Ubuntu 18.04 server VM, may be using virtual box or kvm, whatever you like and make sure you setup that VM with static IP address.

## Install docker
```bash
sudo apt-get update && sudo apt-get install -y apt-transport-https
sudo apt install docker.io
```
Make sure Docker is install properly
```bash
sudo systemctl status docker
```
If docker is not running then start and enable it
```bash
sudo systemctl start docker
sudo systemctl enable docker
```

## Install K8s
```bash
sudo curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
sudo vi /etc/apt/sources.list.d/kubernetes.list

#insert the following one line into kubernetes.list
  deb http://apt.kubernetes.io/ kubernetes-xenial main

# More updates
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl kubernetes-cni
sudo iptables -F
sudo swapoff -a

# Init kubeadm
sudo kubeadm init --pod-network-cidr=10.244.0.0/16

# This will give you kubeadm join info something line this, note this down, this is what u will use from other node to join this master.
kubeadm join 192.168.1.108:6443 --token 95648a.taew3o8khwwkv0i6 --discovery-token-ca-cert-hash sha256:d4fcc4f33213c1c8d1822b8ccc97129962e1d5f0a3928bde85c286e03d4668e5

# More command
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
## Deploy network, You can pickup other network providers, i am going to use flannel
```bash
sudo kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
sudo kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/k8s-manifests/kube-flannel-rbac.yml
```
## All good!!
make sure you got all the infra pods are running.
```bash
kubectl get pods --all-namespaces
```

Now you can create another VM, execute most of the above on it and join the cluster by using master nodes token ... 
```bash
kubeadm join 192.168.1.108:6443 --token 95648a.taew3o8khwwkv0i6 --discovery-token-ca-cert-hash sha256:d4fcc4f33213c1c8d1822b8ccc97129962e1d5f0a3928bde85c286e03d4668e5
```

