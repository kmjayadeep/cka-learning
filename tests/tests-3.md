# Practice tests 3 - Services and networking

## Services

Create a deploy nginx and expose a service on port 80

```
k create deploy nginx --image nginx
k expose deploy nginx --port 80
```

## DNS

Adjust the dns zone of the cluster from `cluster.local` to `my.local`

```
k edit cm coredns -n kube-system
```

replace kubernetes plugin zone `cluster.local` with `my.local`
Check coredns pods to make sure the corefile is refreshed

## CNI

Install calico CNI on the cluster initialized with kubeadm

```
wget https://docs.projectcalico.org/manifests/calico.yaml
```

```
k apply -f calico.yaml
```
