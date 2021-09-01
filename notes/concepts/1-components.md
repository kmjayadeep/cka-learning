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


# Nodes

nodes can be registered by kubelet with predefined taints and labels.
Alternatively, nodes can be added through kubelet. But until it is healty, no pods will be scheduled.

node name should identify the node. This let us delete a machine and start another one and register with the same name. Kubernetes will treat it as the same as before.

Node has 3 addresses -> HostName, InternalIP and ExternalIP

Node has a lease object in kube-node-lease namespace. Kubelet updates
the lease every 10 seconds to indicate that it is alive. If kubelet fails
to send heartbeat for a specified period time, Node controller wil take 
further action to start evicting the pods from the node

From 1.22, Kubelets can run with Swap enabled. We can specify the
swap behaviour in kubelet configuration

Kubelet has an https endpoint which the api server will use to connect
for fetching pod logs, attaching or to do port forwarding

## Conditions

Conditions in the node status contain a list of observed conditions of the node.
This looks like a general pattern used in other resources as well.
A condition will contain a type, message, reason, status (true/false) and timestamps.


# Controller

Controllers usually watch a set of resources and talk to api server
to reconcile current state to desired state for a particular resource
eg: Node controller

# Clout controller manager

This controller embeds cloud specific logic. It includes Node controller,
Route controller and Service controller to manage specific things
with the cloud provider.

