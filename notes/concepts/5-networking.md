# Services, Networking etc

# Services

Service gives a common access point to a set of pods

We can define services without selector. In this case we have to create Endpoint
objects and maintain them manually

Endpoint slices are used to distribute endpoints to multiple resources and thus,
it is more scalable.

* User space proxy mode -> kube proxy catptures the traffic to clusterIP through
  IPTable rules, and proxies the connection to appropriate backend (round robin
  by default). It talks to api server to find the list of backends.
* IPTables mode -> Kube proxy watches endpoints and create IPTable rules to
  forward traffic to the backend sets when there is a connection to the service
  ip and port
* IPVS proxy mode -> provides more options for load balancing


`spec.externalTrafficPolicy` defines how the traffic from external sources are
routed. Default value is `cluster`

`cluster` -> route traffic to all ready endpoints
`local` -> route traffic to only the endpoints in the same node. if there are
no ready pods in the same node, then the traffic is not forwarded

Similarly, we can set internalTrafficPolicy as well.

We can add spec.topologyKeys to route traffic based on the node toplogy of the
cluster.

## Headless services

if the clusteIP is set to none, then the service is called headless. This
service is not handled by kube-proxy.

If there are selectors defined, then the DNS is configured to return A records
corresponding to all the pods backing the service.

If there are no selectors defined, we can either specify a externalName in the
spec, which will be configured as CNAME in the DNS or we can maintain Endpoint
object manually

Headless services are typically useful for databases and latency critical
applications, to avoid an additional network hop through kube-proxy.

## Service types

* ClusterIP -> gives an ip reachable in the cluster
* NodePort -> A port is allocated on each node and routed to the ClusterIP of
  the service. The range is 30000-32767 by default
* LoadBalancer -> Use the cloud provider load balancer to give a static ip and
  route the traffic to NodePort on all the nodes.
* ExternalName -> Creates a CNAME entry in DNS to the spec.externalName field 

# DNS

DNS queries will be expanded to the FQDN based on the resolv.conf injected into
the pod.

* Services have a dns entry for :
  `my-svc.my-namespace.svc.cluster-domain.example`
* Pods have a dns entry for : `pod-ip-address.my-namespace.pod.cluster-domain.example`

We can specify dns config on a per pod basis to configure additional nameservers

Pods can discover services by checking environment variables.
