# Resource limits

We can specify resource request and limits for each containers in a pod.
The schedulers will match it with the available resources per node and 
schedule accordingly. Kubelet makes sure that the container won't use
more resources than what is specified in the limits.

By default we can add limits and requests to  

* cpu
* memory
* hugepages-\*
* ephemeral-storage

Containers will be terminated if it uses more resource than the limit.
It can be evicted if the container is using more memory than requested
and node is out of memory.

# Quotas

ResourceQuotas can be added per namespace to limit the number of api resources
deployed in the namespace.

To create a quota

```
k create quota podq --hard pods=2
```

We can also add `requests.cpu`, `limits.memory` etc. the quota and thus
limiting the total resource limits in a namespace.

`requests.storage` -> is the sum of storage requests across all claims

The events will show if creation of any new objects fail due to quota.

# LimitRange

LimitRange can enforce minimum/maximun/default resource usage per container
in a namespace.

Example:

```
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-limit-range
spec:
  limits:
  - default:
      cpu: 1
    defaultRequest:
      cpu: 0.5
    type: Container
```

This sets `limits.cpu` to `1` and `requests.cpu` to `500m` by default

There can be `default`, `defaultRequest`, `max` and `maxLimitRequestRatio` specified in the limits

If in a pod spec, container limit is specified and request is not specified, 
request wil be aded to match the limit, instead of taking the limitrange defaultRequest.

LimitRange and Quota, in combination is used to restrict resource usage.
LimitRange limits the resource requests per container. Quota limits the total resource usage in a namespace.

# PriorityClass

It is a clusterwide resource which can be used to assign priority to pods. 
Higher priority pods are scheduled sooner than lower priority pods. Also, they can cause
lower priority pods to be evicted if nodes doesn't have enough room to accomodate.
Pods are evicted on the decreasing order of priority if there is a need.

```
k create pc highp --value 1000 --description highPriority
```

To create a pod with this PC, just add the field `priorityClassName` to the pod spec. The priority number
will be added automatically to the pod based on the value

By default, priority is 0 for the pods.
