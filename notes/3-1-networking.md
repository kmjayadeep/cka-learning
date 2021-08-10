# Host Networking configuration

Kubernetes network model
* All pods can talk to all pods without NAT
* All agents in node can talk to all pods in that node

* All containers in a pod can communicate each other through localhost.
  They are on the same network namespace.
* All pods get individual IP addresses from a flat address space
* Ip address of the pod as visible from inside and outside pod are the
  same. There is not NAT involved.
* The `service` abstraction for pods create a virtual IP for a set of
  pods. `kube-proxy` running on each node creates IPTable rules for
  these services to route to correct endpoints


## Pod to pod networking

There is a `veth` pair between each pod network namespace and root
namespace. It allows traffic between node (host) and the pod.

A layer 2 ethernet bridge is added to unite these network segments. One
side of each veth pair, which exists on the root namespace is connected
to the network bridge. This bridge then uses ARP protocol to determine
the MAC addresses corresponds to given IP address. When a data frame (L2)
arrives at the bridge, it sends it to all connected devices and bases on
which device responds to the frame, the IP address of that device is
stored on a lookup table along with the mac address. This lookup table
is used to forward frames in the future based on IP address.

**Across the node**

It depends on the network how the traffic between pods cross the node
boundary. 

Generally, a CIDR is assigned to each node in a cluster. Pod Ips are
allocated from this CIDR. Once traffic reaches a node, it becomes the
responsibility of the node to forward it to the correct pod.

When a frame which is destined at a pod on a different node arrives at
the bridge, the ARP will fail as there are no devices which listen to
that address in the same node. So the traffic is redirected to the host
network interface, which is the default route for network bridge.

At this point, the route leaves the node and enters the network. The
network has to route the traffic to correct node based on the CIDR
assigned to each nodes. The packet enters the root ns and routes to the
network bridge. From there it goes to the correct pod.

### CNI

CNI provides a common api to connect containers to outside network. In
AWS CNI, they attach the nodes to a VPC and pods get ip addresses from
the VPC. This way, pods are automatically reachable through AWS native
way.

CNI is responsible for inserting a network interface to the container,
allocating IP address and setup the routes correctly reach the traffic.
kubernetes calls the CNI plugin with calls such as `ADD`, `DEL` etc on
certain events such as pod creation. CNI is called by kubelet twice, for
loopback and eth0 interface during pod creation.

# Service

`iptables` is used to configure `netfilter` which is built in linux
kernal. Iptables are configured by kube-proxy in each node at the root
namespace. So the load balancing is done by iptables in the host itself.
This makes the service ip reachable from the node itself.

## IPVS

IPVS is a more scalable approach to service networking. It creates a
dummy interface bound to IPVS for each service. And for each endpoint, a
IPVS server is created.

# Ingress

Ingress is used to manage external connectivity to a cluster, usually
HTTP
