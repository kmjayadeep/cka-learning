# Managing nodes

`kubectl drain` drains all pods from the node and makes the node unschedulable

* --ignore-daemonsets flag indicates to ignore daemon pods while draining. Otherwise the daemonset controller will bring them back again, which ignores the schedule rules
* --delete-local-data deletes emptydir volumes attached to the pods in the node

This is an imperative operation which immediately evicts all the pods (excluding daemonsets) and marks the node as unschedulable

`kubectl cordon` will make the node unschedulable for further pods. But doens't drain existing pods.

`kubectl uncordon` makes the node schedulable. This can be used after the drain operation as well, to indicate that the node is ready now

# Remove a worker node from a cluster

Deploy some pods to test

```
k create deploy web --replicas=10 --image=nginx
```

Drain

```
k drain k2 --ignore-daemonsets --delete-local-data
```

Watch the pods getting rescheduled to the existing node and the node status set to SchedulingDisabled

```
k get po -o wide
k get no
```

Bring it back

```
k uncordon k2
```

Run `k delete po --all` to check if pods are getting scheduled to this node as well


Drain again and remove it


```
k delete node k2
```
