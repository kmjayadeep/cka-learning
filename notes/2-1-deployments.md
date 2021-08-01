# Deployments

Deployments are used to create and manage replicasets. It lets you do rollback/rolling updates etc. to the workloads in a streamlined manner.
Typically users deploy applications as `Deployments` rather than as an individual pod even if it has just one replica

* Use `kubectl create deploy` to create deployment spec.

Each time the deployment is updated, it creates a new replicaset, scales it up gradually
while the old replicaset is scaled down gradually. This is only applicable if the changes requires creating a new replicaset
Like, updating pod template

If the updated replicaset matches any of the previously created replicaset, it will scale that up instead
of creating a new replicasets

The flag `--record` adds the command which you ran as an annotation to the resource.
It will be shown as CHANGE-CAUSE while listing rollout history.
This can be used to track how the change happened

`spec.revisionHistoryLimit` indicates how many replicaset history you want to retain. Default is 10

## strategy type `spec.strategy.type`

`Recreate` means all pods will be removed the new pods will be created
`RollingUpdate` [default] rolling update 

Default strategy for deployment rollout is 25% maximum additional pods, 25% max unavailable
(minimum 75% of the pods should be up and running)

## Rollout

* Run `k rollout status deploy/[name]` to check rollout status
* kubectl rollout command is applicable to `deployments`, `daemonsets` and `statefulsets`

To update the image of an existing deployment,

```
kubectl set image deploy/[name] [containerName]=[newImage]
```

You can pause and resume rollouts even while it is in progress using `k rollout pause/resume/restart deploy/[name]` command

You can pause a rollout, Then do all sorts of changes to the deployment resume the rollout to get all changes done at once
If the rollout is paused and you make changes, then UP-TO-DATE count will become 0 for the deployment

cannot rolback a paused rollout until you resume it

When you pause a rollback, `paused:true` is added to corresponding deployment spec.
So we can do it in declarative manner as well!

## Undo

Undo a rollout to a previous revision

```
k rollout undo deploy/web --to-revision=9
```

## Scale

```
k scale deploy [name] --replicas=[count]
```

Scaling the deployment doesn't affect the rollout history.


## HPA

Horizontal pod autoscaler is a way to automically scale deployment when
a certain criteria is met, for example if the pod is using 90% of the cpu allocated

It requires the resrouce limits to be specified. Otherwise it wont work as expected
It also needs metrics server installed

Eg:
```
k autoscale deploy web --min=2 --max=5 --cpu-percent=80
```

## Difference between replicaset, replicationcontroller and deployments

* replicationcontroller is the old implementation and it does what the name suggests
* replicaset is a newer implementation with more options
* deployment is on top of replicaset and it manages rolling updates by scaling/downscaling the 
  underlying replicaset


There are some differences between replicationcontroller and replicaset,
but I don't think they are relevant now.. as most of us use only
deployments. Even replicasets are used rarely
