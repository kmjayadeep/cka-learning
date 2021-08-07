# Workloads

Built in workloads

* Deployments and Replicaset - for managing stateful resources
* StatefulSet - for managing stateful workloads
* Daemonset - for running pods per node
* Jobs and Cronjobs - for managing pods which run to completion

# Pods

Pods contain multilple containers in the same network namespace.
So they share the same network interface and ip address

Static pods are directly managed by the kubelet. Kubelet takes care
of restarting them if it fails. They are bound to a node. Static
pods are usually used for control plane components in a self hosted
setup.

Contaier probes like livenessProbe, readinessProbe and startupProbe
are done by kubelet

`RuntimeClass` lets you configure pods with different container
runtime configurations

## Phases

* Pending -> Waiting to run
* Running -> ALl containers started and atlest one of them is running
  or in the process of starting or restarting
* Succeeded -> All containers exited with success code
* Failed -> All containers terminated and atleast one of them failed
* Unknown -> Can't get pod status for some reason

Container states -> Waiting, Running, Terminated

`restartPolicy` : Applied to all containers in the pod
* `Always` -> Restart when container exists (whether success or failure)
* `OnFailure` -> Restart when container exits with failure
* `Never` -> never restart

`restartPolicy` of Deployment will always be always
For Daemonsets, it can be anything
For Jobs, it can be OnFailure or Never

clearly documented in openshift docs
<https://docs.openshift.com/online/pro/architecture/core_concepts/pods_and_services.html#admin-manage-pod-restart>

## Init containers

* run to completion
* multilple init containers run in order
* have the same structure as normal containers
* support resource limits and requests
* doesn't support probes
* they follow restartPolicy `onFailure` or `never`. If the pod restartPolicy is
  always, then init containers will follow onFailure

## TopologySpreadConstraints

Based on node labels (topology key), we can control the spread of pods based on
topology of the nodes (eg: Zones or availability zones)

## Ephemeral Containers (new in 1.22)

We can attach temporary containers to a running pod for debugging purposes.
It is otherwise not possible to add/remove containers from a pod


`shareProcessNamespace` in pod.spec is used to make processes visible to all
containers in a pod. Pod needs `SYS_PTRACE` capability to add this
Container filesystems are visible to other containers through /proc/$pid/root
link

## Running pods directly

```
k run nginx --image=nginx --rm -ti --restart=Never -- echo "hello wordddld"
```

# Deployment

`k rollout` commands are used to do rolling updates and deployment

Deployment used `replicasets` to manage multiple replicas of pod

# Replicaset

manage multiple replicas of pods and maintain the desired number of replica
count.

`controller.kubernetes.io/pod-deletion-cost` annotation is used to set
preference over which pod to be deleted in case of scale down

Deployment is preferred over using Replicaset on its own

# StatefulSet

it provides
* stable unique network identifiers
* stable persistent storage
* ordered graceful deployment
* ordered rolling updates

We can specify spec.volumeClaimTemplates in StatefulSet. The controller will
create PVCs for the pod automatically based on this template and create volumes
on the pod spec matching the PVCs

# Daemonset

Runs one pod per node

We can add nodeSelector and node affinity to pod if we don't want to run in all
nodes

Daemonsets tolerate a set of predefined taints by default and the pods are not
evicted on these events

A Daemonset can be easily created using kubectl by creating Deployment through
`kubectl create` and then changing the kind to Daemonset.

# Job

Run jobs until they are complete

`ttlSecondsAfterFinished` is used to clean up jobs after a certain period
after completion. It will clean up corresponding pods as well. This is taken
care by TTLController

# CronJob

Runs `Job` objects at a predefined time interval (cron expression).
It js jobTemplate and schedule in the spec
