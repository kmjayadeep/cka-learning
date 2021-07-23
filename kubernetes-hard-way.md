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
lxc launch ubuntu:18.04 envoy --profile kubernetes

lxc launch ubuntu:18.04 controller-0 --profile kubernetes
lxc launch ubuntu:18.04 controller-1 --profile kubernetes
lxc launch ubuntu:18.04 controller-2 --profile kubernetes

lxc launch ubuntu:18.04 worker-0 --profile kubernetes
lxc launch ubuntu:18.04 worker-1 --profile kubernetes
lxc launch ubuntu:18.04 worker-2 --profile kubernetes
```
