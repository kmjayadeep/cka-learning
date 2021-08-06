# Taints and tolerations

Taints have key, value and an effect

eg:

```
kubectl taint nodes node1 key1=value1:NoSchedule
```

If a taint is added to a node, then only pods which has a matching toleration for the taint will be allowed onto the node.
If the effect is noExecute, then even the running pods are evicted from the node

Toleration example in pod spec

```
tolerations:
- key: "key1"
  operator: "Equal"
  value: "value1"
  effect: "NoSchedule"
```

Taints can be removed by adding `-` at the end of the above kubectl taint command

There are some taints which are added automatically in case of certain scenarios - like Diskpressure, pidpressure etc.
You can add tolerations to keep the pod in the same node for a period of time when this occurs.

For daemonsets, some tolerations are aded automatically to ensure that they are not affected by some automatic taints.
