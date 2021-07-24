# LXC and LXD

LXD is a set linux tools to create and manage containers. We can use it to create lightweight containers for running kubernetes
LXC is the client tool to access LXD. It can be used to create vms as well


## Install

```
sudo pacman -S lxc lxd
```

You may need to replace `iptables` with `iptables-nft` while installation


## Initialize lxd

```
sctl start lxd
# Add user to lxd group
sudo usermod -a -G lxd $(whoami)
newgrp lxd
# Initialize with defaults
sudo lxd init --auto
```

## Create kubernetes profile from the profile file

```
lxc profile create kubernetes
lxc profile edit kubernetes < kubernetes.profile
```

A restart might be good to avoid some issues

## Troubleshooting tips

[https://www.adaltas.com/en/2020/02/04/install-debug-lxd-k8s/]
