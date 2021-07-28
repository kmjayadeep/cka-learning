# Kubeadm

It is best to use katakoda to learn kubeadm. Can also use LXC (check lxc.md in the root dir)

## Installing using kubeadm

```
sudo apt update
sudo apt-get install -y apt-transport-https ca-certificates curl
```

```
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

```
sudo kubeadm init
```

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Install pod network, flannel, weave, calico etc

# Proxy

Running `k proxy` will expose api server on `localhost:8001`. We can then use curl commands to interact with api server. Very useful!

```
curl localhost:8001/api/v1/pods
```

# Katakoda - setting up cluster using kubeadm

<https://www.katacoda.com/courses/kubernetes/launch-cluster>

Similar to above steps. Kubeadm was preinstalled. Token was hardcoded rather than genereated by kubeadm

Steps

1. Kubeadm init with token
1. Copy kubeconfigs
1. Install weave network


On worker,

1. kubeadm join with token (show token by running `kubeadm token list` in master)

Check nodes

```
k get no
```

Deploy pod to test

```
kubectl create deployment http --image=katacoda/docker-http-server:latest
```

Deploy dashboard

```
kubectl apply -f dashboard.yaml
```
