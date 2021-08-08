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

# Ingress

Ingress is to provide external access to the application. It can do load
balancing, path based routing etc.

An ingress controller should be installed in order for ingress to work

install nginx ingress controller
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.48.1/deploy/static/provider/baremetal/deploy.yaml
```

To configure TLS, A secret should be created with tld key and crt. Then this
secret name can be referenced in the secretName field of secret configuration in
ingress.

if there are multiple ingress controllers in the cluster, we can add an
annotation to the ingress resource `ingress.class` with the ingress class of the
controller which we would like to handle the ingress resource.

# Network Policy

It is used to control how pods communicate with other pods and external
networks.
network policy is implemented by Network Plugin. So only works on supported CNIs

With network policy we can define Ingress and Egress rules for each pod based on
label selectors. It sort of acts as a firewall for pods.

The namespaceselector and podselector can be used together to target a specific
set of pods in a specific set of namespaces. If they are specified in different
`from` or `to` blocks, then it becomes an `OR` condition.
