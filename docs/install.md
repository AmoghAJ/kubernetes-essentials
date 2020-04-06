
# Kubernetes Installation

## Install Docker
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

sudo apt-get update

sudo apt-get install -y docker-ce=18.06.1~ce~3-0~ubuntu

sudo apt-mark hold docker-ce
```

Verify Installation
```
sudo docker version
```

## Kubertenes components installation
```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

cat << EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

sudo apt-get update

sudo apt-get install -y kubelet=1.12.7-00 kubeadm=1.12.7-00 kubectl=1.12.7-00

sudo apt-mark hold kubelet kubeadm kubectl
```

Verify kubeadm version
```
kubeadm version
```

## Bootstapping the cluster
On kubernetes master
```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```
Then setup kubeconfig
> following instructions will be on stdout once you run initialize pod network
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Add nodes to cluster
> following instructions will be on stdout once you run initialize pod network
```
sudo kubeadm join $some_ip:6443 --token $some_token --discovery-token-ca-cert-hash $some_hash
```

Verify from master:
```
kubectl get nodes
```
> All nodes would be in not ready state as there is no network between them.


## Configure Flannel
Flannel provide the cluster networking capabilities.
All nodes in cluster:
```
echo "net.bridge.bridge-nf-call-iptables=1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```
Master node:
```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml
```

Verify:
```
kubectl get nodes
```
> All nodes would be in ready state as network is been established between them.

You can check all the pods under kube-system namespace, which are kube system related pods.
```
kubectl get pods -n kube-system
```