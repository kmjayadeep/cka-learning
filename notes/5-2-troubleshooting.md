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
