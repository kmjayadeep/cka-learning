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
