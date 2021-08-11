# CoreDNS

In k8s, CoreDNS is used for DNS services.
It comes with a standard corefile including a set of plugins

It is `kubernetes` plugin which provides DNS entries for services
and pods in the cluster

This configuration is defined in a configmap `coredns` in `kube-system`
namespace.

CoreDNS can be customized by adjusting this configmap

To adjust the zone for the cluster (by default it is `cluster.local`),
edit the configmap and replace the `cluster.local` with the required
zone.

Services get a dns host name in the form

`name.namespace.svc.cluster.local`

Pods get dns hostname in the form

`ip.namespace.pod.cluster.local`
