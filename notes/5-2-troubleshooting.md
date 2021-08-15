# Troubleshooting

* `k describe` command tells a great deal about a resource if it is not
* `k events` can show some system events which will contain some useful
  info for debugging

* `k debug` command can be used to debug a running application by
  copying the pod, copying container, adding ephemeral container etc.

* `k debug node/nodename` can be used to run a container on a host
  network, ipc and pid namespaces, with a specified image


To run a interactive busybox pod,

```
kubectl run -it --rm --restart=Never busybox --image=busybox sh
```

Use `wget -O-` to make a request and print to stdout. This can be useful
on alpine like containers where curl is not present

Check service endpoint list, kube-proxy status and dns configuration if the service is not
reachable

`crictl` can be used to check pods, containers etc on a host machine

If metrics server is installed, `k top` commands can be used to check
resource usage per node/pod/container.

`/var/log` folder containers all the logs necessary to debug any cluster
wide component failure

# Cluster component failure

Usually the cluster components are run as static pods. The manifests for
them can be found under /etc/kubernetes/manifests folder. If there is
any misconfiguration, it can be modified and the kubelet will
automatically relaunch them.

Nodes can become unhealthy if the kubelet is not running or it is not
able to reach apiserver.

Failure of controllers can cause the underlying resources not to work as
expected. Eg, if deployment controller is not running, the corresponding
replicaset wont get created on new deployment.

In case of cluster failure, we can restore etcd snapshot and the cluster
will be back to its previous state more or less.

`k events` will be really helpful here

# Networking troubleshooting

mostly the logs will tell if something wrong

If service is not reachable,
* Check DNS configuration, check with ip address
* Check `Endpoint` for the service
* Check kube-proxy is running
* Check endpoints are reachable

Kubernetes wont be able to run pods until CNI is running.

We can examine IPTable rules to check if kube-proxy configured
everything correctly.

Check CNI logs for errors

Use `iptables-save` command to dump the iptable rules and examine them.
Alternatively, use `iptables -L`
