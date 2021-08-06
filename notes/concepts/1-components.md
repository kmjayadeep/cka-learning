# Kubernetes Components

# Control Plane

1. `etcd` -> Key value store. Source of truth for everything in the cluster
1. `kube-apiserver` -> A Stateless api server which acts as a frontend to the control plane.
  All other components talk to the api server (usually though TLS certs)
1. `kube-scheduler` -> Watches non-assigned pods assign nodes to the pod based on various factors.
1. `kube-controller-manager` -> Contains in-built controllers (eg. Deployment, Node, StatefulSet etc.). Controllers watch their
 resources and take action to reconcile current state to the desired state.
1. `cloud-controller-manager` -> Talks to cloud provider to do cloud specific actions like creating load balancer Ips, allocating node etc

# Node Components

1. `kubelet` -> An agent which runs on each node, talks to api server and container runtime to ensure that pods are running
as expected.
1. `container-runtime` -> The component which is responsible for running containers. Eg: Docker, CRI-O, Podman
1. `kube-proxy` -> Network proxy which runs on each node to implement Kubernetes service concept.

# Others

1. `DNS` -> DNS server to resolve pod and service addresses.
1. `CNI` -> Takes care of assigning IPs, adding proper routes etc.
