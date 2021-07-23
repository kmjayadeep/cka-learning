# Kubernetes the Hard way

Following the tutorial <https://github.com/kelseyhightower/kubernetes-the-hard-way> and <https://www.nicktriller.com/blog/notes-on-kubernetes-the-hard-way/> to learn kubernetes the hard way


## Setup LXD and LXC 

<lxc.md>

## Tools

```
yay cfssl
```

## Setup machines

```
lxc launch ubuntu:18.04 haproxy --profile kubernetes
or
lxc launch images:centos/7 haproxy --profile kubernetes

lxc launch ubuntu:18.04 controller-0 --profile kubernetes
lxc launch ubuntu:18.04 controller-1 --profile kubernetes
lxc launch ubuntu:18.04 controller-2 --profile kubernetes

lxc launch ubuntu:18.04 worker-0 --profile kubernetes
lxc launch ubuntu:18.04 worker-1 --profile kubernetes
lxc launch ubuntu:18.04 worker-2 --profile kubernetes
```

## Setup haproxy

```
exc exec haproxy -- bash
```

```
apt install haproxy net-tools
```

Edit haproxy config and add the following config (replace controller ips with ips from `lxc ls` )

```
vim /etc/haproxy/haproxy.cfg
```

```
frontend k8s
        bind 10.13.184.147:6443
        mode tcp
        default_backend k8s

backend k8s
        balance roundrobin
        mode tcp
        option tcplog
        option tcp-check
        server controller-0 10.13.184.16:6443 check
        server controller-1 10.13.184.65:6443 check
        server controller-2 10.13.184.184:6443 check
```

```
systemctl enable haproxy
systemctl start haproxy
```
