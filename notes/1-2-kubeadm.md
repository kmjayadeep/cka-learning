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

# Multi master setup in lxc

Start lxd

```
sudo systemctl start lxd
```

Create containers. (check lxc.md for profile)
```
lxc launch ubuntu:18.04 haproxy --profile kubernetes
lxc launch ubuntu:18.04 controller-0 --profile kubernetes
lxc launch ubuntu:18.04 controller-1 --profile kubernetes
```

Configure haproxy

```
lxc exec haproxy -- bash
apt update
apt install haproxy
```

Haproxy config `/etc/haproxy/haproxy.cfg`. Add the following (replace backend ip addresses)

```
frontend kubernetes
        mode tcp
        option tcplog
        bind 0.0.0.0:6443
        default_backend kubernetes

backend kubernetes
        mode tcp
        balance roundrobin
        server master1 10.13.184.252:6443 check
        server master0 10.13.184.104:6443 check
```

Control planes

note: didnt work properly... need to check
```
sudo kubeadm init --control-plane-endpoint "10.13.184.246:6443" --upload-certs
```

# Multi master setup in ubuntu 18.04

Cleanup existing

```
sudo kubeadm reset
```

Haproxy

```
sudo apt install haproxy
sudo vim /etc/haproxy/haproxy.cfg
```

Add the following

```
frontend kubernetes
        mode tcp
        option tcplog
        bind 0.0.0.0:7443
        default_backend kubernetes

backend kubernetes
        mode tcp
        balance roundrobin
        server master1 10.2.33.51:6443 check
        server master0 10.2.33.52:6443 check
```

```
sudo systemctl enable haproxy
sudo systemctl start haproxy
```

Kubeadm

```
sudo kubeadm init --control-plane-endpoint "10.2.33.51:7443" --upload-certs --pod-network-cidr=10.244.0.0/16
```
(pod-network-cidr is required for the pod networking plugin to work, flannel or calico. Check their docs properly before setting cidr)

Copy kubeconfig

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Install calico

```
k apply -f https://docs.projectcalico.org/manifests/calico.yaml
```
Flannel didn't work, because there was some calico stuff on the cluster previously and it was messing up with the new installation


On other node

```
sudo kubeadm join 10.2.33.51:7443 --token s0i645.v6kzs8tb06zr9wj9 \
    --discovery-token-ca-cert-hash sha256:4bec906d1c5bd6629f0d3532c388e5c6d09373814200e7c9b5897de22dd402b2 \
    --control-plane --certificate-key 311ff4de6dbdbe97440bc64d272044bd7f3f78ae4511a367a81a13bc1342f561
```

Untaint master nodes

```
kubectl taint node nodename node-role.kubernetes.io/master:NoSchedule-
```

# Setting up a cluster on Digitalocean

* Setup doctl

```
doctl auth init
```

* Create dropplets


```
doctl compute droplet create k1 --size s-2vcpu-2gb --image ubuntu-20-04-x64 --region nyc1 --ssh-keys f6:70:54:43:ee:b8:c6:36:ec:41:a8:76:8c:69:b2:73
doctl compute droplet create k2 --size s-2vcpu-2gb --image ubuntu-20-04-x64 --region nyc1 --ssh-keys f6:70:54:43:ee:b8:c6:36:ec:41:a8:76:8c:69:b2:73
```

```
doctl compute droplet list
```

SSh and install the stuff


```
ssh root@64.227.29.105 # master
ssh root@147.182.186.192 # worker
```

```
apt update
swapoff -a # Disable swap
```

Check and enable `br_netfilter`

```
lsmod | grep br_netfilter
modprobe br_netfilter
```

```
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl --system
```

* Install and start docker


```
apt install docker.io -y
systemctl enable docker
docker ps
```

* kubeadm and stuff

```
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
```

```
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

On master node
```
kubeadm init --pod-network-cidr=192.168.0.0/16
```

Install calico

````
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
````

To setup worker node

```
kubeadm token create --print-join-command
```

Copy that command and run on worker node
which looks like this

```
kubeadm join 64.227.29.105:6443 --token [token] --discovery-token-ca-cert-hash sha256:[hash]
```

Test

```
k create deploy web --image=nginx --replicas=2
k scale deploy web --replicas=10
k get po -o wide
k expose deploy web --type=NodePort --port=80
curl localhost:[NodePort]
```

## Cleanup doctl nodes

```
doctl compute droplet list
doctl compute droplet delete [id]
```

# Kubadm version upgrade

Setup Digitalocean servers and install prerequisites as above (except kubernetes stuff)

Install kubernetes v1.18.13-00 on master
 
```
apt install -y kubeadm=1.18.13-00 kubelet=1.18.13-00 kubectl=1.18.13-00
```

Join the worker node using kubeadm join command as given above


Do the upgrade

```
apt update
apt-cache madison kubeadm
```

```
apt install -y kubeadm=1.19.13-00
```

```
kubeadm upgrade plan
kubeadm upgrade apply v1.19.13
```

Upgrade kubelet

```
kubectl drain k1 --ignore-daemonsets
apt install kubelet=1.19.13-00 kubectl=1.19.13-00
systemctl daemon-reload
systemctl restart kubelet
```

enable scheduling

```
kubectl uncordon k1
```

Now `k get no` should show the current node as v1.19.13

## Upgrade worker nodes

Better untaint the master first


```
k taint node k1 node-role.kubernetes.io/master:NoSchedule-
```

```
apt install -y kubeadm=1.19.13-00
```

Run upgrade checks
```
kubeadm upgrade node
```

Drain node and upgrade kubelet

```
kubectl drain k2 --ignore-daemonsets
apt install kubelet=1.19.13-00 kubectl=1.19.13-00
systemctl daemon-reload
systemctl restart kubelet
```

Enable scheduling

```
k uncordon k2
```
