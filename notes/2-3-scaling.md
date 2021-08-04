# Scaling apps, creating self healing, roboust application deployments.

## Scaling

You can easily scale a deployment using `kubectl scale` command

```
k scale deploy [name] --replicas=2
```
The same command can be used to scale replication controllers,
replicasets and statefulsets

If `--current-replicas` is added to the scale command, then it will 
be scaled only if the current count of replicas match this.

## HPA - Horizontal Pod Autoscaler

It needs `metrics-server` or something equivalent to be present. Then only
it can fetch the metrics for each pod.

It also needs resource requests to be set for each container in the pod.

HPA periodically checks the resource consumption of each pod and takes
decision on the replicaCount based on the HPA configuration.
HPA controlls the `replicaCount` of the deployement.

Algorithm:
```
desiredReplicas = ceil[currentReplicas * ( currentMetricValue / desiredMetricValue )]
```

By default, hpa takes cpu target utilization percentage as 80 if nothing is specified

eg:
```
k autoscale deploy php-apache --min=2 --max=10
```

Autoscaling based on memory is supported in `autoscaling/v2beta1` api group
